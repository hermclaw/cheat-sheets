---
icon: lucide/git-fork
---

# Jujutsu (jj)

A modern, Git-compatible version control system.

## Fetching a Pull Request

```bash
# Fetch a PR branch from a remote
jj git fetch --remote hermclaw --branch "feat/custom-volume-mounts"
```

## Switching to a PR Branch

```bash
# Create a new working-copy commit on top of the fetched branch
jj new feat/custom-volume-mounts@hermclaw
```
