---
layout: post
title: "Decomposing a monorepo"
date: 2022-02-18 -0800
comments: false
categories: [Git,tfs]
---

So we've got a big ol' monorepo that's stored in TFS.  Most of it is getting converted to Git as we trasition away from Team Foundation Version Control (TFVC).  The monorepo is about 3GB and has about a decade of history.  Some of the projects that were bolted onto the sides of the repo are good candidates to break out into their own small repositories in Git.  But how exactly can I do that and keep all the history?

I've got 3 solutions so far:

* Use [Git-TFS](https://github.com/git-tfs/git-tfs) to transform a TFS sub-folder into a Git repository
* Use the BFG tool to remove folders/files that you don't like from a Git repo
* Use built-in Git tools to `filter-branch`

## Using Git-TFS to transform a TFS sub-folder into a Git repository

Since I've got the option of doing a custom migration from TFS to Git I can add a complicated regex with negative lookaheads to pick out all the subfolders I want to keep.  Then Git-TFS has to walk through all the commits since the dawn of time and only keep the files that get through the ignore filter.  That might take a day but I think I'll get exactly what I want.

```PowerShell
#choco install git -y
#choco install gittfs -y
Start-Transcript git.txt -Force
$destination = "pad"
Remove-Item -Recurse -Force $destination -Verbose
$ignoreRegex = @(
    "[Pp]ackages/", # nuget package folder
    "[Dd]ebug/", # build output folders
    "bin/[Rr]elease/",
    "\.vssscc", # TFS source control stuff
    "\.vspscc",
    "\.vs/", # Visual Studio folder for IIS settings and stuff
    "\.vscode/",
    "node_modules/", # npm packages folder
    "AppLibs/(?!PAD).*", # only convert the AppLibs/PAD folder and ignore everthing else
    "Database/(?!PAD|Deploy).*",
    "Websites/(?!PAD).*",
    "Common/",
    "PoC/",
    "Reports/",
    "Tools/"
) -join '|'
git tfs clone http://tfs.myorg.net/tfs/myorg $/Path/To/Main/mymonorepo $destination --debug --branches=none --from=348573 --ignore-regex=$ignoreRegex
Set-Location $destination
git lfs migrate import --above=50MB
git tfs cleanup --debug
git gc --aggressive
git branch -m master main
Set-Location ..
Stop-Transcript
```

## Use the BFG tool to remove folders/files that you don't like from a Git repo

You can use the [BFG tool](https://github.com/rtyley/bfg-repo-cleaner) to purge unwanted folders and files from history.  Not my favorite approach because you have to list all the folders you don't want and sometimes the code you _do_ want has a folder of the same name.  It doesn't allow you to specify paths and will grump at you if you try.

I tried this but lost history that I didn't want to part with (e.g. Activity and Tools).  On the plus side, it was done in about a minute.

```PowerShell
# https://github.com/rtyley/bfg-repo-cleaner
Start-Transcript git.txt -Force
# Error: *** Can only match on filename, NOT path *** - remove '/' path segments
$foldersToDelete = @(
    "Activity",
    "Policy",
    "Rating",
    "CorpMisc",
    "Admin",
    "PoC",
    "Reports",
    "Tools"
) -join ','
# git clone https://github.com/myorg/mymonorepo
# git remote remove origin
"java.exe -jar bfg-1.14.0.jar --delete-folders {$($foldersToDelete)} --no-blob-protection mymonorepo" | Out-File -FilePath "slimdown-pad.cmd" -Force
& ".\slimdown-pad.cmd"
cd mymonorepo
# git wipe
git reflog expire --expire=now --all
git gc --prune=now --aggressive
Stop-Transcript
```

## Use built-in Git tools to filter-branch

Git's built-in tooling comes with a lot of warnings.  When you try to run `git filter-branch` it says that it will be very, very, very slow and potentially mangle your history.

This is currenly taking me hours to do one path.  I'm probably not going to get to the end of this exercise

```cmd
rem https://www.atlassian.com/blog/git/tear-apart-repository-git-way
rem create a repo with just this folder
git filter-branch --subdirectory-filter Websites/PAD -- main

rem remove one path and its history from this repository
git filter-branch --index-filter "git rm -r --cached --ignore-unmatch AppLibs/Activity" --prune-empty
```
