# Git-Standup is cool

But I wanted something slimmer.

    [alias]
        lga = log --graph --oneline --all --decorate --abbrev-commit
        standup = "!f() { USERNAME=$(git config user.name); if [ $(date +%u) -eq 1 ]; then git --no-pager lga --since=\"last friday\" --author=\"$USERNAME\"; else git --no-pager lga --since=\"1 day ago\" --author=\"$USERNAME\"; fi; }; f"