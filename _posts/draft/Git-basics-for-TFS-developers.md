---
layout: post
title: "Git basics for TFS developers"
date: 2020-02-18 -0800
comments: false
categories: [Git,tfs]
---

    ```powershell
    #choco install git -y
    #choco install gittfs -y
    Start-Transcript 'git-from-clone.txt' -Force
    $destination = "code-git-from-clone"
    Remove-Item -Recurse -Force $destination -Verbose
    git clone http://tfs.yourdomain.net/tfs/foo/bar/_git/code $destination
    Set-Location $destination
    git tfs bootstrap --debug
    git checkout -b develop
    git tfs pull --debug --rebase
    Set-Location ..
    Stop-Transcript
