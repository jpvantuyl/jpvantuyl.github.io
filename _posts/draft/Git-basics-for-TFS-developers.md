---
layout: post
title: "Git basics for TFS developers"
date: 2020-02-18 -0800
comments: false
categories: [Git,tfs]
---

Git is a tool that provides source control.  Our current TFS server supports TF for source control and Git.

## Installation

I recommend one of two options to install Git on your machine.

There’s a .MSI that you can download and install from <https://gitforwindows.org/.>  The tool should prompt you for how you want it installed (just accept the defaults).

The alternative is to use the Chocolatey package manager for Windows to install Git from the commandline ([link](https://chocolatey.org/packages/git)).

![poshgit](/images/content/2020/poshgit.jpg)

If you’re going the Chocolatey route try `choco install poshgit` for PowerShell integration and `choco install gittfs --version=0.29.0` for TFS integration.

With either approach you should end with git.exe available on your path from some sort of commandline.  As long as Git is available from the commandline then all other tooling built on top of Git should work.


Setup
Git requires some configuration.  At a minimum you need to set your full name and email address.  These are included in each commit.  If you start Visual Studio 2019 and point it at a Git repository then you should be prompted like so:


There’s a number of other configuration options you can specify.  The easiest way to get some helpful defaults is to type git config --global -e into a command prompt.  This should open the global .gitconifg file in your default text editor.  Then you can copy/paste/edit based on the following:

    # This is Git's per-user configuration file.
    [user]
    # Please adapt and uncomment the following lines:
        #name = John Van Tuyl
        #email = jpvant@pemco.com
        useConfigOnly = true
    [core]
        # These CR/LF settings seem to work well for repositories that will only be edited on Windows machines.
        autocrlf = false
        safecrlf = false
        excludesfile = H:\\My Documents\\gitignore_global.txt
        editor = '"C:\\Program Files\\Microsoft VS Code\\Code.exe"'
        filemode = false
    [help]
        autocorrect = 20  # git will re-submit the autocorrected command after 2 seconds
    [color]
        ui = auto
    [filter "lfs"]
        clean = git lfs clean %f
        smudge = git lfs smudge %f
        required = true
    [alias]
        co = checkout
        ec = config --global -e
        up = !git pull --rebase --prune $@ && git submodule update --init --recursive
        tfup = !git tfs pull --rebase --ignore-not-init-branches --debug $@
        ct = tfs ct
        rct = !git tfup && git tfs rcheckin --debug
        cob = checkout -b
        cm = !git add -A && git commit -m
        save = !git add -A && git commit -m 'SAVEPOINT'
        wip = !git add -u && git commit -m "WIP" 
        undo = reset HEAD~1 --mixed
        amend = commit -a --amend
        wipe = !git add -A && git commit -qm 'WIPE SAVEPOINT' && git reset HEAD~1 --hard
        bclean = "!f() { git branch --merged ${1-master} | grep -v " ${1-master}$" | xargs -r git branch -d; }; f"
        bdone = "!f() { git checkout ${1-master} && git up && git bclean ${1-master}; }; f"
        migrate = "!f(){ CURRENT=$(git symbolic-ref --short HEAD); git checkout -b $1 && git branch --force $CURRENT ${3-'$CURRENT@{u}'} && git rebase --onto ${2-master} $CURRENT; }; f"
        shelve = "!f(){ CURRENT=$(git symbolic-ref --short HEAD); git tfs shelve $CURRENT -f; }; f"
        userstats = shortlog -sne
        lga = log --graph --oneline --all --decorate --abbrev-commit
        standup = "!f() { USERNAME=$(git config user.name); if [ $(date +%u) -eq 1 ]; then git --no-pager lga --since=\"last friday\" --author=\"$USERNAME\"; else git --no-pager lga --since=\"1 day ago\" --author=\"$USERNAME\"; fi; }; f"
        retrospective = "!f() { USERNAME=$(git config user.name); git --no-pager lga --since=\"2 weeks ago\" --author=\"$USERNAME\"; }; f"
    [push]
        default = simple
    [http]
        sslVerify = false  # Our internal servers use self signed certs
    [url "https://"]
        insteadOf = git://  # force https:// protocol instead of git:// because of the self signed cert
    [url "https://github.com"]
        insteadOf = git@github.com  # allow pushing to github
    [fetch]
        prune = true

Editors
You will need a shell sometimes but it’s more productive to do a lot of your day to day operations in an editor.


Visual Studio 2019 has nice Git integration in the Team Explorer bar.


Visual Studio Code also has a nice built in UI for Git.


Really, you can take your pick.  There’s also:

Source Tree from Atlasian

GitHub Desktop

Vim

etc.


Two Stage Commit
Committing your files to source control is the meat-and-potatoes of software development.  Let’s take Visual Studio 2019 as our IDE and walk through the steps:

Make some changes to some files.  Doesn’t have to be inside your .sln file, Git pays attention to every file in the entire repository.  If you changed it then it should be available for you to commit.

Go to the Team Explorer tab and select the Changes button.


 

Double click any file to see a diff of what changed


 

It is required to put in a commit message.  After that you can click the Commit All button.  If you only wanted to commit some of your changes then you can right click the file and choose to stage it.

Do this as many times as you have patience for.  Git shines when you make a lot of tiny commits locally.  They’re cheap to create and each one is a point you can roll back to, investigate history for, or eventually reformat into one big commit to share with everyone.  We’re still in phase 1 of the two stage commit where you’re storing changes in the Git database on your machine but haven’t shared them with anyone else.

Phase two begins when you like what you’ve got and you’re willing to share.  Once you share commits with others on your team it can be painful to change them.

Before we can share your changes it’s best to pull down the latest to ensure that your repository is up to date.  If there are merge conflicts you want to handle them here.  You can do a git pull from the commandline or select Pull from the menu in your IDE.


 


 

Next either git push or click the Push button in your IDE.  Some editors also have a Sync option that does a pull and then a push.

Astute observers will have noticed a Fetch option.  A git fetch is like a pull but it just stores new commits in your database without applying them to whatever branch you’re working on.  Using pull is preferable because it will update files on disk and force you to deal with other people’s work if there was going to be a conflict.


Branches!
When work heats up and you’ve got more than one thing on your plate Git branches can come to your rescue.  Just like local commits that aren’t shared with anyone Git gives you local branches just for you.

Branching by story allows you to keep all the commits for each of your work items nice and tidy.  When you’re stumped on a coding challenge you can bounce over to something easier by switching to that branch.  All your problems will still be there when you get back.

Just like commits, branches can be shared with your friends if you’ve got some long-running thing that needs that level of isolation.
