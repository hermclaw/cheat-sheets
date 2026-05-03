---
title: "GitHub CLI Cheat Sheet"
description: "Comprehensive GitHub CLI (gh) reference covering repos, PRs, issues, Actions, releases, and API access."
layout: default
---

## Installation & Auth

```bash
# Install (common methods)
brew install gh                     # macOS
apt install gh                     # Debian/Ubuntu
dnf install gh                     # Fedora
winget install GitHub.cli          # Windows

# Authentication
gh auth login                      # Interactive login
gh auth login --web                # Browser-based login
gh auth login --with-token < token.txt  # Token-based login
gh auth status                     # Check auth status
gh auth logout                     # Log out
gh auth refresh                    # Refresh credentials
gh auth token                      # Print auth token
gh auth setup-git                  # Configure git credentials
```

## Repo Management

### Create

```bash
gh repo create                     # Interactive creation
gh repo create my-project          # Create in current directory
gh repo create my-project --public
gh repo create my-project --private
gh repo create my-project --description "Description"
gh repo create --source=. --push   # From existing directory
gh repo create my-project --clone  # Create and clone
```

### Clone & Fork

```bash
gh repo clone owner/repo           # Clone repo
gh repo clone owner/repo my-dir    # Clone into directory
gh repo fork                       # Fork current repo
gh repo fork owner/repo            # Fork and clone
gh repo fork --clone               # Fork and clone automatically
```

### View & Info

```bash
gh repo view                       # View current repo
gh repo view owner/repo            # View specific repo
gh repo view --web                 # Open in browser
gh repo view --json name,url       # JSON output
gh repo view --jq '.name'          # JSON with jq filter

gh repo list                       # Your repos
gh repo list owner                 # User/org repos
gh repo list --limit 10            # Limit results
gh repo list --json name,desc      # JSON with specific fields
gh repo list --forked              # Forks only
gh repo list --source              # Non-forks only
gh repo list --public              # Public repos
gh repo list --private             # Private repos
```

### Edit & Config

```bash
gh repo edit --description "New description"
gh repo edit --name "new-name"
gh repo edit --default-branch main
gh repo edit --visibility private
gh repo edit --add-topic python,cli
gh repo edit --remove-topic old-topic

gh repo delete                     # Delete current repo (with confirmation)
gh repo delete owner/repo --yes    # Delete without confirmation
```

## Pull Requests

### Create

```bash
gh pr create                       # Interactive (prompts for fields)
gh pr create --title "Title" --body "Description"
gh pr create --draft               # Create as draft
gh pr create --head feature-branch --base main
gh pr create --label bug,urgent    # With labels
gh pr create --assignee user1,user2
gh pr create --reviewer user1
gh pr create --web                 # Open browser to create

gh pr create --fill                # Auto-fill title/body from commits
gh pr create --body-file desc.md   # Body from file
```

### List & View

```bash
gh pr list                         # Open PRs in current repo
gh pr list --state all             # All PRs
gh pr list --state closed          # Closed PRs
gh pr list --state merged          # Merged PRs
gh pr list --author user1          # By author
gh pr list --assignee user1        # By assignee
gh pr list --label bug             # By label
gh pr list --head feature-branch   # By head branch
gh pr list --base main             # By base branch
gh pr list --limit 20
gh pr list --json number,title,author,state

gh pr view 123                     # View specific PR
gh pr view 123 --web               # Open in browser
gh pr view 123 --json title,body,state,url
gh pr view 123 --comments          # Include comments
```

### Checkout & Diff

```bash
gh pr checkout 123                 # Checkout PR branch locally
gh pr diff 123                     # View diff
gh pr diff 123 --name-only         # Changed files only
```

### Merge & Review

```bash
gh pr merge 123                    # Interactive merge
gh pr merge 123 --merge            # Merge commit
gh pr merge 123 --squash           # Squash merge
gh pr merge 123 --rebase           # Rebase merge
gh pr merge 123 --auto             # Auto-merge if checks pass
gh pr merge 123 --delete-branch    # Delete branch after merge

gh pr review 123                   # Submit review
gh pr review 123 --approve         # Approve
gh pr review 123 --request-changes # Request changes
gh pr review 123 --comment         # Leave comment
gh pr review 123 --body "LGTM!"    # With body
gh pr reviews 123                  # List reviews on a PR
```

### Checks & Status

```bash
gh pr checks 123                   # View CI status
gh pr checks 123 --watch           # Watch until complete
gh pr status                       # Status of all PRs
gh pr ready 123                    # Check if PR is ready to merge
```

### Misc PR Commands

```bash
gh pr close 123                    # Close PR
gh pr reopen 123                   # Reopen PR
gh pr edit 123 --title "New title" --body "New body"
gh pr edit 123 --add-label bug
gh pr edit 123 --remove-label bug
gh pr edit 123 --add-assignee user1
gh pr edit 123 --remove-assignee user1
gh pr comment 123 --body "Comment text"
gh pr lock 123                     # Lock conversation
gh pr unlock 123
```

## Issues

### Create & View

```bash
gh issue create                    # Interactive
gh issue create --title "Bug" --body "Description"
gh issue create --label bug --assignee user1
gh issue create --web

gh issue view 42                   # View issue
gh issue view 42 --web
gh issue view 42 --json title,body,labels,state,comments
gh issue view 42 --comments        # Include comments
```

### List

```bash
gh issue list                      # Open issues
gh issue list --state all
gh issue list --state closed
gh issue list --author user1
gh issue list --assignee user1
gh issue list --label bug
gh issue list --label "priority:high"
gh issue list --milestone v1.0
gh issue list --limit 20
gh issue list --json number,title,labels
```

### Manage

```bash
gh issue close 42                  # Close issue
gh issue reopen 42                 # Reopen
gh issue edit 42 --title "New title"
gh issue edit 42 --add-label bug,urgent
gh issue edit 42 --remove-label bug
gh issue edit 42 --add-assignee user1
gh issue edit 42 --remove-assignee user1
gh issue edit 42 --add-project "Board"

gh issue comment 42 --body "Comment"
gh issue lock 42
gh issue unlock 42
gh issue delete 42                 # Delete (repo owner only)
```

### Transfer & Pin

```bash
gh issue transfer 42 owner/repo2   # Transfer to another repo
gh issue pin 42                    # Pin issue
gh issue unpin 42
```

## GitHub Actions

### Workflows

```bash
gh workflow list                   # List workflows
gh workflow view workflow.yml      # View workflow
gh workflow view workflow.yml --yaml  # View raw YAML
gh workflow enable workflow.yml    # Enable workflow
gh workflow disable workflow.yml   # Disable workflow
gh workflow run workflow.yml       # Trigger workflow
gh workflow run workflow.yml -f name=value  # With inputs
```

### Runs

```bash
gh run list                        # List recent runs
gh run list --workflow "CI"        # Specific workflow
gh run list --branch main          # Specific branch
gh run list --status success       # By status
gh run list --status failure
gh run list --limit 10

gh run view 12345                  # View run details
gh run view 12345 --web            # Open in browser
gh run view 12345 --log            # View full log
gh run view 12345 --log-failed     # Failed steps only

gh run watch                       # Watch current run
gh run watch 12345                 # Watch specific run

gh run rerun 12345                 # Rerun failed run
gh run rerun 12345 --failed        # Rerun only failed jobs
gh run cancel 12345                # Cancel run
gh run download 12345              # Download artifacts
gh run download 12345 -n artifact_name
```

### Artifacts

```bash
gh run download                    # Download latest artifacts
gh run download -R owner/repo      # From specific repo
gh run download -n artifact-name   # Specific artifact
```

## Releases

```bash
gh release list                    # List releases
gh release list --limit 10
gh release view v1.0.0             # View release
gh release view v1.0.0 --web
gh release view latest             # Latest release
gh release download v1.0.0         # Download assets
gh release download v1.0.0 -p "*.tar.gz"  # Pattern match

gh release create v1.0.0           # Interactive (from git tag)
gh release create v1.0.0 --title "v1.0.0" --notes "Release notes"
gh release create v1.0.0 --notes-file RELEASE.md
gh release create v1.0.0 ./dist/*.tar.gz  # With assets
gh release create v1.0.0 --draft   # Draft release
gh release create v1.0.0 --prerelease  # Pre-release
gh release create v1.0.0 --target main    # Target branch

gh release edit v1.0.0 --title "New Title"
gh release edit v1.0.0 --notes "Updated notes"
gh release edit v1.0.0 --draft    # Convert to draft
gh release edit v1.0.0 --tag v1.0.1  # Re-tag

gh release delete v1.0.0          # Delete release
```

## Gists

```bash
gh gist list                       # List your gists
gh gist list --public              # Public only
gh gist list --secret              # Secret only
gh gist list --limit 10

gh gist create file.py             # Create from file
gh gist create file1.py file2.md   # Multiple files
gh gist create --public file.py    # Public gist
gh gist create --desc "Description" file.py
gh gist create - << 'EOF'          # From stdin
echo "hello world"
EOF

gh gist view <id>                  # View gist
gh gist view <id> --web            # Open in browser
gh gist edit <id> file.py          # Edit gist
gh gist delete <id>                # Delete gist
gh gist clone <id>                 # Clone gist
```

## API Access

### gh api

```bash
# GET requests
gh api /repos/owner/repo          # Get repo info
gh api /repos/owner/repo/pulls    # List PRs
gh api /repos/owner/repo/issues?state=closed
gh api /user                      # Authenticated user info
gh api /user/repos?per_page=100   # Paginated
gh api /repos/owner/repo --jq '.stargazers_count'  # Extract field

# POST requests
gh api /repos/owner/repo/issues \
  -f title="New issue" \
  -f body="Description" \
  -f labels="bug,help wanted"

# POST with JSON body
gh api /repos/owner/repo/issues \
  --input data.json

# PUT / PATCH / DELETE
gh api --method PATCH /repos/owner/repo -f description="Updated"
gh api --method DELETE /repos/owner/repo/labels/old-label

# Raw output
gh api /repos/owner/repo --paginate  # Auto-paginate
gh api /repos/owner/repo -H "Accept: application/vnd.github.v3+json"

# GraphQL
gh api graphql -f query='
  query {
    viewer {
      login
      repositories(first: 10, orderBy: {field: UPDATED_AT, direction: DESC}) {
        nodes {
          name
          url
        }
      }
    }
  }
'
```

## Search

```bash
gh search repos "python cli" --limit 10
gh search repos "python cli" --language python --sort stars
gh search repos --owner owner "topic"

gh search issues "bug in login" --repo owner/repo
gh search issues "label:bug state:open" --repo owner/repo
gh search issues "author:user1" --limit 20

gh search prs "is:open is:pr review:required" --repo owner/repo
gh search commits "fix" --repo owner/repo

gh search code "function main" --language python --repo owner/repo

# All searches support --json and --jq flags
gh search repos "python cli" --json fullName,description,stargazersCount
```

## Aliases & Extensions

### Aliases

```bash
gh alias set prs "pr list --state all --limit 10"
gh alias set co "pr checkout"
gh alias set pv "pr view --web"
gh alias set issues "issue list --state all --limit 10"
gh alias list
gh alias delete prs
```

### Extensions

```bash
gh extension list                  # List installed
gh extension install owner/extension  # Install extension
gh extension search "keyword"     # Search extensions
gh extension create my-ext        # Create new extension
gh extension remove my-ext        # Remove extension
```

## Misc

```bash
gh browse                          # Open repo in browser
gh browse file.py                  # Open specific file
gh browse --branch feature         # Specific branch
gh browse --commit abc123          # Specific commit

gh project list                    # List projects
gh project view 1                  # View project

gh secret list                     # List secrets
gh secret set SECRET_NAME --body "value"
gh secret set SECRET_NAME < file.txt
gh secret delete SECRET_NAME

gh variable list                   # List variables
gh variable set VAR_NAME --body "value"

gh ssh-key add key.pub             # Add SSH key to GitHub
gh ssh-key list                    # List SSH keys

gh api rate-limit                  # Check rate limit
gh --version                       # Version info
gh help <command>                  # Command help
gh <command> --help                # Same
```

## Common Workflows

### PR from Fork

```bash
gh repo fork owner/repo --clone
cd repo
git checkout -b feature-branch
# Make changes
git add . && git commit -m "feat: add feature"
git push -u origin feature-branch
gh pr create --title "Add feature" --body "Description"
```

### Review & Merge PRs

```bash
gh pr list --reviewer @me          # PRs waiting for my review
gh pr view 123 --comments          # Read PR + comments
gh pr diff 123                     # View diff
gh pr review 123 --approve         # Approve
# or
gh pr review 123 --request-changes --body "Please fix X"
# or merge directly:
gh pr merge 123 --squash --delete-branch
```

### Debug Failed CI

```bash
gh run list --workflow "CI" --status failure
gh run view 12345 --log-failed     # Failed step logs
gh run watch                       # Watch re-run
```
