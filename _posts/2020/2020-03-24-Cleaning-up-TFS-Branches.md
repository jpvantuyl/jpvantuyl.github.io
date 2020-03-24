---
layout: post
title: "Cleaning up TFS Branches"
date: 2020-03-24 -0800
comments: false
categories: [tfs]
---

From time to time it's nice to clean up old TFS builds and branches.  These things accumulate over time and slowly increase the cognitive load required to find _your_ branch when patching or whatever.

## Use automation to build a list of branches

First we need an inventory of what we've got to delete.  I'm a fan of [git-tfs](https://github.com/git-tfs/git-tfs) so I use the command `git tfs list-remote-branches https://tfs.mycompany.net/tfs/mycode | clip` to get a list of feature branches, release branches, etc.  I pipe it to clip to get it on the clipboard from my PowerShell session then paste it into VS Code.  Then I do a small regex workout to remove characters; it's good to use that muscle from time to time.

```text
TFS branches that could be cloned:

$/mycode/Main [*]
|
+- $/mycode/Releases/MyApp/MyApp.54
|  |
|  +- $/mycode/Releases/MyApp/MyApp.54.7

Cloning root branches (marked by [*]) is recommended!


PS:if your branch is not listed here, perhaps you should convert its containing folder into a branch in TFS:
-> Open 'Source Control Explorer' and for each folder corresponding to a branch, right click on the folder and select 'Branching and Merging' > 'Convert to branch'.
```

When you're done cutting you should have a list of branches each on their own line that you can paste into Excel to create a CSV file.  You'll also want to create a column called *ShouldKeep* which is defaulted to TRUE.

## Use automation to build a list of recently deployed branches

We have a database with build information in it here.  I queried the db for the list of builds to keep and merged that with my hand-crafted list of branches.

```powershell
$tfsBranches = Get-Content -Path '.\TFSBranches.csv' |
    ConvertFrom-Csv

$SqlConnection = New-Object System.Data.SqlClient.SqlConnection

$SqlConnection.ConnectionString = "Server=dev.mycompany.net;Database=BuildDb;Integrated Security=True"
$SqlConnection.Open()
$SqlCmd = New-Object System.Data.SqlClient.SqlCommand
$SqlCmd.CommandText = @"
SELECT [BuildLabel]
FROM [BuildDB].[dbo].[vw_BuildsToNotDelete]
WHERE BuildLabel != ''
"@
$SqlCmd.Connection = $SqlConnection
$reader = $SqlCmd.ExecuteReader()
$table = New-Object System.Data.DataTable
$table.Load($reader)
$SqlConnection.Close()

$buildsToNotDelete = @($table.BuildLabel | Select-Object -Unique)

$tfsBranches = $tfsBranches |
    ForEach-Object {
        $branch = $_.Branch
        $branchLabel = ($branch -split '/') | select -Last 1
        $builds = $buildsToNotDelete | ? { $_ -like "*$branchLabel*" }
        [PSCustomObject]@{
            Branch = $branch;
            ShouldKeep = [bool]$builds;
        }
    }

$tfsBranches |
    ConvertTo-Csv -NoTypeInformation |
    Out-File -FilePath '.\TFSBranches.csv' -Encoding utf8
```

## Ask your co-workers to tell you if you're about to shoot yourself in the foot

Communication is an important skill at work.  Never more so than when you're doing to destroy something that took weeks to put together.  Or months.  Or years.  If you're releasing to production more frequently than that then you probably don't use TFS and don't have these problems.

In order to be effective, your communication has to generate engagement.  You need to have your co-workers actually look at the list and correct you if you're about to delete something of value.  This is harder than one might expect even in the center of a COVID-19 pandemic where people have a lot of time on their hands.  Follow up with your peeps until you're confident that things aren't going to go _too_ sideways.

## Commence with the foot-shooting

I've cooked up a script that works for me.  Given a workspace with almost everything mapped in TFS but almost no files on disk it will get latest onto the disk, delete the files in that branch, then commit the delete operation to source control.

```powershell
# place this file in a folder that has a workspace that lines up with the $localpath variables listed
# you should open a Visual Studio 2019 command prompt then navigate to this directory
# that should make tf.exe accessible
# execute this script with `powershell.exe .\DeleteBranch.ps1` from the commandline
# You'll also need a TFSBranches.csv in the same folder

Start-Transcript DeleteBranch.txt -Append

$tfsBranches = Get-Content .\TFSBranches.csv |
ConvertFrom-Csv

$tfsBranchesToDelete = $tfsBranches |
Where-Object {
    $_.ShouldKeep -eq "FALSE"
}

"[$(Get-Date)] Found $($tfsBranchesToDelete.Count) branches to delete"

$tfsBranchesToDelete |
ForEach-Object {
    $b = $_
    try {
        $localPath = $b.Branch -replace '\$/mycode/', ''
        "[$(Get-Date)] Deleting $localPath"
        &tf.exe vc get /recursive $localPath
        if (Test-Path $localPath) {
            &tf.exe vc delete /recursive $localPath
            &tf.exe vc checkin /comment:"Delete $localPath" /noprompt
        }
    }
    catch {
        "[$(Get-Date)] Error: $_"
    }
}

Stop-Transcript
```

## Extra Credit: delete some old builds

I've used [TfsTeamProjectManager](https://github.com/jelledruyts/TfsTeamProjectManager) to delete XAML build definitions with some success.  Just check the box to delete the build via the UI.
