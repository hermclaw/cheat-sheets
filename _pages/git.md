---
title: "Git Cheat Sheet"
description: "Comprehensive Git reference covering commands, branching, remotes, stashing, resetting, and common workflows."
layout: default
---

## Setup & Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.autocrlf input    # Line ending normalization
git config --global core.editor "vim"
git config --global pull.rebase true       # Rebase on pull by default
git config --global alias.co checkout      # Aliases
git config --global alias.br branch
git config --global alias.st status
```

- `--global` → user-wide (`~/.gitconfig`)
- `--local` → repo-only (`.git/config`)
- `--system` → machine-wide

## Init & Clone

```bash
git init                         # Initialize in current directory
git init my-project              # Initialize in new directory
git clone https://github.com/user/repo.git
git clone --depth 1 <url>        # Shallow clone (latest commit only)
git clone --branch <name> <url>  # Clone specific branch
git clone --recurse-submodules <url>  # Clone with submodules
```

## Staging & Committing

```bash
git status                       # Show working tree status
git add file.txt                 # Stage specific file
git add .                        # Stage all changes (tracked + new)
git add -p                       # Stage interactively (hunk by hunk)
git add -A / git add --all       # Stage everything (including deletions)

git commit -m "message"          # Commit staged changes
git commit -am "message"         # Stage tracked files + commit
git commit --amend               # Amend last commit (message or content)
git commit --amend --no-edit     # Amend without changing message
git commit --allow-empty -m "msg"  # Empty commit (useful for triggers)
```

### Gitignore

```
# .gitignore syntax
*.log              # Pattern matching
build/             # Ignore directory
!important.log     # Negation (track this file)
*.py[cod]          # Brackets
__pycache__/
.env
```

```bash
git check-ignore -v <file>      # Debug which rule ignores a file
```

## Branching

```bash
git branch                       # List local branches
git branch -r                   # List remote branches
git branch -a                   # List all branches
git branch <name>               # Create branch
git branch -d <name>            # Delete branch (safe — merged)
git branch -D <name>            # Force delete (even if unmerged)
git branch -m <old> <new>       # Rename branch

git checkout <name>             # Switch branch (legacy)
git switch <name>               # Switch branch (preferred)
git switch -c <name>            # Create and switch
git switch -c <name> <start>    # Create from specific ref

git merge <branch>              # Merge branch into current
git merge --no-ff <branch>      # Always create merge commit
git merge --squash <branch>     # Squash merge (no commit yet)
```

## Rebase

```bash
git rebase main                 # Rebase current branch onto main
git rebase -i HEAD~5            # Interactive rebase (last 5 commits)
git rebase --continue           # Continue after resolving conflict
git rebase --abort              # Cancel rebase
git rebase --skip               # Skip current commit
```

### Interactive Rebase Commands

- `p` / `pick` — keep commit as-is
- `r` / `reword` — keep commit, edit message
- `e` / `edit` — pause to amend commit
- `s` / `squash` — meld into previous commit
- `f` / `fixup` — meld into previous (discard message)
- `d` / `drop` — remove commit

### Cherry-Pick

```bash
git cherry-pick <sha>           # Apply specific commit
git cherry-pick <sha1> <sha2>   # Apply multiple commits
git cherry-pick -x <sha>        # Include original SHA in message
git cherry-pick --abort         # Cancel in-progress cherry-pick
```

## Remote Operations

```bash
git remote -v                   # List remotes
git remote add origin <url>     # Add remote
git remote remove <name>        # Remove remote
git remote rename <old> <new>   # Rename remote

git fetch origin                # Fetch from remote
git fetch --all --prune         # Fetch all, prune deleted branches

git pull                        # Fetch + merge
git pull --rebase               # Fetch + rebase (preferred)
git pull origin main            # Pull specific branch

git push                        # Push to tracked remote
git push -u origin main         # Push and set upstream tracking
git push origin <branch>        # Push specific branch
git push --force                # Force push (dangerous!)
git push --force-with-lease     # Safer force push
git push origin --delete <br>   # Delete remote branch
```

## Stashing

```bash
git stash                       # Stash current changes
git stash push -m "desc"        # Stash with message
git stash list                  # List stashes
git stash pop                   # Apply latest stash and remove it
git stash apply                 # Apply latest stash, keep it
git stash apply stash@{2}       # Apply specific stash
git stash drop stash@{1}        # Remove specific stash
git stash clear                 # Clear all stashes
git stash show                  # Show stash contents
git stash show -p               # Show stash diff
```

## Resetting

```bash
git reset HEAD~1                # Mixed reset: unstage last commit
git reset --soft HEAD~1         # Soft: keep changes staged
git reset --mixed HEAD~1        # Mixed: keep changes unstaged (default)
git reset --hard HEAD~1         # Hard: discard all changes

git checkout -- <file>          # Discard working directory changes
git restore <file>              # Discard working directory changes (preferred)
git restore --staged <file>     # Unstage file (preferred)
```

### Reset Modes Summary

| Mode | Staged | Working Dir | Commit Removed? |
|------|--------|-------------|-----------------|
| `--soft` | ✅ kept staged | ✅ unchanged | Yes |
| `--mixed` | ❌ unstaged | ✅ unchanged | Yes |
| `--hard` | ❌ gone | ❌ gone | Yes |

## Log & Diff

```bash
git log                         # Full commit history
git log --oneline               # Compact: short SHA + message
git log --oneline --graph       # ASCII graph
git log --oneline --graph --all # All branches graph
git log -5                      # Last 5 commits
git log --since="2 weeks ago"
git log --author="name"
git log --grep="pattern"
git log -p                      # Include patches
git log --stat                  # Include file change stats

git diff                        # Unstaged changes
git diff --staged               # Staged changes
git diff HEAD                   # All changes vs last commit
git diff main..feature          # Diff between branches
git diff --name-only            # Changed file names only
```

## Reflog

```bash
git reflog                      # Log of HEAD movements
git reflog show HEAD@{5}        # Specific reflog entry
# Recover lost commits:
git checkout -b recovery <sha>  # From reflog SHA
```

## Bisect

```bash
git bisect start                # Start bisect session
git bisect bad                  # Mark current commit as bad
git bisect good <sha>           # Mark known-good commit
# Git will check out middle commit — test it, then:
git bisect good                 # or git bisect bad
# Repeat until found
git bisect reset                # End session, return to start
```

## Submodules

```bash
git submodule add <url> <path>  # Add submodule
git submodule init              # Initialize submodules
git submodule update            # Update submodules
git submodule update --init --recursive  # Clone + update all
git submodule status            # Show submodule status
git submodule foreach 'git pull origin main'  # Update all
```

## Tagging

```bash
git tag                         # List tags
git tag v1.0.0                  # Lightweight tag
git tag -a v1.0.0 -m "Release"  # Annotated tag
git tag -d <tag>                # Delete local tag
git push origin --tags          # Push all tags
git push origin <tag>           # Push specific tag
git push origin --delete <tag>  # Delete remote tag
```

## Common Workflows

### Undo Last Commit (keep changes)

```bash
git reset --soft HEAD~1
```

### Undo Last Commit (discard everything)

```bash
git reset --hard HEAD~1
```

### Squash Last N Commits

```bash
git rebase -i HEAD~N
# Mark all but first as 'squash'
```

### Sync Fork with Upstream

```bash
git remote add upstream <url>
git fetch upstream
git rebase upstream/main
git push origin main --force-with-lease
```

## Common Gotchas

- **Detached HEAD**: You're not on any branch. Commit changes to a new branch: `git switch -c <name>`
- **Commit to wrong branch**: `git stash`, switch branch, `git stash pop`
- **Force push rewrites history** — never do this on shared public branches
- **`git add .` vs `git add -A`**: `-A` also stages deletions in subdirectories
- **Pull vs fetch**: `pull` auto-merges, `fetch` is safer (review before merging)
- **Merge conflicts**: Edit conflicted files, `git add` them, then `git commit`
- **Case sensitivity**: Git is case-sensitive; `File.txt` and `file.txt` differ on Linux but not macOS/Windows by default
- **Large files**: Use Git LFS for files > 100 MB — regular Git will bloat the repo
- **Amended commits have new SHA**: Never amend pushed commits that others have based work on
