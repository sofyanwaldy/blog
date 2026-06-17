---
title: "Git Complete Cheatsheet"
description: "A practical Git cheatsheet for daily development — commands, branching, merging, rebasing, stashing, logging, undoing mistakes, and collaboration workflows."
date: 2026-06-17
tags: ["git", "cheatsheet", "developer-tools", "version-control"]
url: /git
draft: false
---

# Git Complete Cheatsheet

Git tracks **snapshots** of your project, not diffs. Every commit is a full picture of your entire project at that moment. Understanding this changes how you think about branching, merging, and history.

This cheatsheet is designed for daily development work — a reference you'd actually bookmark and come back to.

---

## Table of Contents

- [Core Concepts](#core-concepts)
- [Configuration](#configuration)
- [Basic Workflow](#basic-workflow)
- [Branching](#branching)
- [Merging](#merging)
- [Rebasing](#rebasing)
- [Remote Repositories](#remote-repositories)
- [Stashing](#stashing)
- [Logging and History](#logging-and-history)
- [Undoing Things](#undoing-things)
- [Cherry-pick](#cherry-pick)
- [Tags](#tags)
- [Git Diff](#git-diff)
- [.gitignore Patterns](#gitignore-patterns)
- [Worktrees](#worktrees)
- [Bisect](#bisect)
- [Practical Workflows](#practical-workflows)
- [Mini Cheat Table](#mini-cheat-table)

---

## Core Concepts

Before running commands, understand Git's three areas:

```text
Working Directory  →  Staging Area (Index)  →  Repository (.git)
      ↑                     ↑                        ↑
  Your files          What goes into          Committed history
  on disk             the next commit         (permanent snapshots)
```

**Working Directory** is where you edit files. It's just your project folder.

**Staging Area** (also called the Index) is a buffer between your working directory and the repository. You explicitly choose what goes into the next commit by staging files. This is Git's killer feature — you can commit only *part* of your changes.

**Repository** is the `.git` directory. It holds all committed snapshots, branches, tags, and configuration.

### What is a Commit?

A commit is a snapshot of the entire project plus metadata:

```text
commit 3a1f2b9
├── tree (snapshot of all files)
├── parent (previous commit)
├── author: Sofyan Waldy <sofyan@example.com>
├── date: 2026-06-17
└── message: "Add user authentication"
```

Every commit has a SHA-1 hash (like `3a1f2b9c...`). This hash is computed from the content, so identical content always produces the same hash.

### What is a Branch?

A branch is just a **pointer** to a commit. That's it. Creating a branch is instantaneous because Git only creates a 41-byte file (the hash + newline).

```text
main    → commit C
feature → commit C  (both point to the same commit right after branching)
```

### What is HEAD?

`HEAD` is a pointer to the **current branch** (which itself points to a commit). When you checkout a branch, `HEAD` moves to that branch.

```text
HEAD → main → commit C
```

If you checkout a specific commit (not a branch), you're in **detached HEAD** state — you're not on any branch.

---

## Configuration

Git configuration has three levels:

| Level | Flag | File | Scope |
|-------|------|------|-------|
| System | `--system` | `/etc/gitconfig` | All users on the machine |
| Global | `--global` | `~/.gitconfig` | Your user account |
| Local | `--local` | `.git/config` | Current repository only |

Local overrides global, global overrides system.

### Essential Setup

```bash
# Identity (required — every commit uses this)
git config --global user.name "Sofyan Waldy"
git config --global user.email "sofyan@example.com"

# Default branch name (avoid the old 'master' default)
git config --global init.defaultBranch main

# Default editor
git config --global core.editor "nvim"

# Line endings (critical for cross-platform teams)
git config --global core.autocrlf input    # macOS/Linux
git config --global core.autocrlf true     # Windows

# Enable colored output
git config --global color.ui auto

# Set default pull behavior to rebase (cleaner than merge commits)
git config --global pull.rebase true

# Push only the current branch by default
git config --global push.default current

# Auto-setup remote tracking on first push
git config --global push.autoSetupRemote true
```

### Useful Aliases

```bash
# Short status
git config --global alias.st "status -sb"

# Pretty one-line log
git config --global alias.lg "log --oneline --graph --all --decorate"

# Amend without editing the message
git config --global alias.amend "commit --amend --no-edit"

# Unstage everything
git config --global alias.unstage "reset HEAD --"

# Last commit details
git config --global alias.last "log -1 HEAD --stat"

# List all aliases
git config --global alias.aliases "config --get-regexp ^alias"

# Show diff of staged changes
git config --global alias.staged "diff --cached"

# Delete merged branches (except main and current)
git config --global alias.cleanup "!git branch --merged | grep -v '\\*\\|main\\|master' | xargs -n 1 git branch -d"
```

### View Configuration

```bash
# List all settings with their origin
git config --list --show-origin

# Check a specific setting
git config user.email

# Edit global config in your editor
git config --global --edit
```

---

## Basic Workflow

### Starting a Project

```bash
# Create a new repository
git init my-project
cd my-project

# Clone an existing repository
git clone https://github.com/user/repo.git

# Clone into a specific folder
git clone https://github.com/user/repo.git my-folder

# Clone only the latest commit (faster for large repos)
git clone --depth 1 https://github.com/user/repo.git

# Clone a specific branch
git clone -b develop https://github.com/user/repo.git
```

### Staging Files

```bash
# Stage a specific file
git add README.md

# Stage multiple files
git add src/index.js src/utils.js

# Stage all changes in the current directory (recursively)
git add .

# Stage all changes in the entire repository
git add -A

# Stage only tracked files (ignore new untracked files)
git add -u

# Interactive staging — pick individual hunks to stage
git add -p
```

#### Interactive Staging with `git add -p`

This is one of Git's most powerful features. It shows each change and asks what to do:

```text
@@ -10,6 +10,8 @@ function processUser(user) {
+  validateEmail(user.email);
+  sanitizeInput(user.name);
   const result = saveUser(user);
Stage this hunk [y,n,q,a,d,s,e,?]?
```

| Key | Action |
|-----|--------|
| `y` | Stage this hunk |
| `n` | Skip this hunk |
| `q` | Quit (don't stage remaining hunks) |
| `a` | Stage this hunk and all remaining hunks |
| `s` | Split into smaller hunks |
| `e` | Manually edit the hunk |

This lets you make a single logical commit even when your file has multiple unrelated changes.

### Committing

```bash
# Commit staged changes with a message
git commit -m "Add email validation to user signup"

# Commit with a multi-line message
git commit -m "Add email validation" -m "Validates format and checks for disposable providers"

# Stage all tracked files and commit in one step
git commit -am "Fix typo in error message"

# Open editor for a detailed commit message
git commit

# Commit with a specific author
git commit --author="Partner <partner@example.com>" -m "Pair programming session"

# Create an empty commit (useful for triggering CI/CD)
git commit --allow-empty -m "Trigger deployment pipeline"
```

#### Writing Good Commit Messages

```text
Add OAuth2 authentication for API endpoints

- Implement token-based auth using JWT
- Add refresh token rotation for security
- Include rate limiting per API key
- Update API documentation with auth headers

Closes #142
```

The convention:
- **First line**: imperative mood, ≤50 characters (like a subject line)
- **Blank line** separating subject from body
- **Body**: explain *what* and *why*, not *how* (the code shows how)

### Checking Status

```bash
# Full status
git status

# Short status (much more readable)
git status -sb
```

Short status output explained:

```text
## main...origin/main [ahead 2]
 M src/utils.js          # Modified but not staged
M  src/index.js          # Modified and staged
A  src/newfile.js        # New file, staged
?? docs/notes.txt        # Untracked file
MM src/config.js         # Staged, then modified again
```

The two columns: left = staging area, right = working directory.

---

## Branching

Branches are cheap. Use them liberally. Every feature, bug fix, and experiment should get its own branch.

### Creating Branches

```bash
# Create a new branch (stays on current branch)
git branch feature/user-auth

# Create and switch to it immediately
git switch -c feature/user-auth

# Older syntax (still works)
git checkout -b feature/user-auth

# Create a branch from a specific commit
git branch hotfix/login-crash abc1234

# Create a branch from a tag
git branch release/v2.1 v2.0.0

# Create a branch that tracks a remote branch
git switch -c feature/api origin/feature/api
```

### Switching Branches

```bash
# Switch to an existing branch
git switch main

# Switch to the previous branch (like cd -)
git switch -

# Older syntax
git checkout main
```

> **Why `git switch` over `git checkout`?** The `checkout` command does too many things — switching branches, restoring files, detaching HEAD. Git 2.23 introduced `switch` (for branches) and `restore` (for files) to make things clearer.

### Listing Branches

```bash
# List local branches (* marks current)
git branch

# List remote branches
git branch -r

# List all branches (local + remote)
git branch -a

# List branches with last commit info
git branch -v

# List branches already merged into current branch
git branch --merged

# List branches NOT yet merged
git branch --no-merged

# List branches containing a specific commit
git branch --contains abc1234
```

### Deleting Branches

```bash
# Delete a merged branch (safe)
git branch -d feature/user-auth

# Force delete an unmerged branch (destructive)
git branch -D experiment/crazy-idea

# Delete a remote branch
git push origin --delete feature/user-auth

# Alternative syntax for deleting remote branch
git push origin :feature/user-auth
```

### Renaming Branches

```bash
# Rename the current branch
git branch -m new-name

# Rename a specific branch
git branch -m old-name new-name

# Rename and update remote
git branch -m old-name new-name
git push origin --delete old-name
git push origin new-name
git push origin -u new-name
```

---

## Merging

Merging combines the work from two branches. Git decides the merge strategy automatically.

### Fast-Forward Merge

When the target branch has no new commits since the feature branch started, Git just moves the pointer forward. No merge commit is created.

```text
Before:
main:    A --- B
                \
feature:         C --- D

After git merge feature (from main):
main:    A --- B --- C --- D
```

```bash
git switch main
git merge feature/user-auth
# Output: "Fast-forward"
```

### Three-Way Merge

When both branches have new commits, Git creates a **merge commit** with two parents.

```text
Before:
main:    A --- B --- E
                \
feature:         C --- D

After git merge feature (from main):
main:    A --- B --- E --- M (merge commit)
                \         /
feature:         C --- D
```

```bash
git switch main
git merge feature/user-auth
# Output: "Merge made by the 'ort' strategy."
```

### Merge Options

```bash
# Merge with a custom commit message
git merge feature/user-auth -m "Merge user authentication feature"

# Force a merge commit even if fast-forward is possible
# (useful for preserving feature branch history)
git merge --no-ff feature/user-auth

# Merge but don't commit yet (inspect the result first)
git merge --no-commit feature/user-auth

# Abort a merge in progress (if there are conflicts)
git merge --abort

# Check if a branch can be merged cleanly without actually merging
git merge --no-commit --no-ff feature/user-auth
git merge --abort   # undo the test merge
```

### Resolving Merge Conflicts

When Git can't auto-merge, it marks conflicts in the file:

```text
<<<<<<< HEAD
const API_URL = "https://api.production.com";
=======
const API_URL = process.env.API_URL || "https://api.staging.com";
>>>>>>> feature/config-env
```

- `<<<<<<< HEAD`: your current branch's version
- `=======`: separator
- `>>>>>>> feature/config-env`: incoming branch's version

**Resolution steps:**

```bash
# 1. See which files have conflicts
git status

# 2. Open each conflicted file and edit manually
#    Remove the conflict markers and keep the right code

# 3. After fixing, stage the resolved files
git add src/config.js

# 4. Complete the merge
git commit
# Git auto-generates a merge commit message

# Alternative: accept one side entirely
git checkout --ours src/config.js     # keep current branch version
git checkout --theirs src/config.js   # keep incoming branch version
```

### Merge Strategies

```bash
# Merge but if there are conflicts, automatically prefer our changes
git merge -X ours feature/user-auth

# Merge but prefer their changes on conflicts
git merge -X theirs feature/user-auth

# "Ours" strategy — record a merge but completely discard the other branch's changes
# (different from -X ours — this ignores ALL changes, not just conflicts)
git merge -s ours feature/deprecated-api
```

---

## Rebasing

Rebasing **replays** your commits on top of another branch, creating a linear history. It rewrites commit hashes.

### Basic Rebase

```text
Before:
main:    A --- B --- E
                \
feature:         C --- D

After git rebase main (from feature):
main:    A --- B --- E
                      \
feature:               C' --- D'  (new commits, same changes)
```

```bash
# From your feature branch
git switch feature/user-auth
git rebase main
```

**C' and D'** are *new commits* — same changes but different hashes because their parent changed. The original C and D are orphaned (eventually garbage collected).

### Interactive Rebase

Interactive rebase lets you edit, squash, reorder, or drop commits. This is how you clean up messy history before merging.

```bash
# Rebase the last 4 commits interactively
git rebase -i HEAD~4

# Rebase everything since branching from main
git rebase -i main
```

Git opens your editor with something like:

```text
pick a1b2c3d Add user model
pick e4f5g6h Add user controller
pick i7j8k9l Fix typo in user model
pick m0n1o2p Add user validation

# Commands:
# p, pick   = use commit
# r, reword = use commit, but edit the commit message
# e, edit   = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup  = like "squash", but discard this commit's message
# d, drop   = remove commit
# x, exec   = run command
```

#### Common Interactive Rebase Scenarios

**Squash "fix typo" into the original commit:**

```text
pick a1b2c3d Add user model
fixup i7j8k9l Fix typo in user model     ← changed from pick to fixup
pick e4f5g6h Add user controller
pick m0n1o2p Add user validation
```

**Reword a commit message:**

```text
reword a1b2c3d Add user model             ← changed from pick to reword
pick e4f5g6h Add user controller
pick m0n1o2p Add user validation
```

**Reorder commits:**

```text
pick m0n1o2p Add user validation           ← moved up
pick a1b2c3d Add user model
pick e4f5g6h Add user controller
```

**Drop a commit entirely:**

```text
pick a1b2c3d Add user model
drop e4f5g6h Add user controller           ← this commit disappears
pick m0n1o2p Add user validation
```

### Handling Rebase Conflicts

```bash
# When a conflict occurs during rebase, fix it then:
git add src/conflicted-file.js
git rebase --continue

# Skip this commit entirely
git rebase --skip

# Abort the rebase and go back to the state before
git rebase --abort
```

### Rebase vs Merge

| Aspect | Merge | Rebase |
|--------|-------|--------|
| History | Preserves true history with merge commits | Creates linear, clean history |
| Commit hashes | Preserved | Rewritten (new hashes) |
| Conflicts | Resolve once | May need to resolve per-commit |
| Shared branches | Safe | **Dangerous** — don't rebase shared branches |
| Use case | Final integration | Cleaning up before merge |

**The Golden Rule**: Never rebase commits that have been pushed to a shared branch. You'd be rewriting history that others depend on.

```bash
# Safe: rebase your local feature branch onto main
git switch feature/my-work
git rebase main

# DANGEROUS: rebasing main after others have pulled it
git switch main
git rebase feature/something   # DON'T DO THIS
```

### Autosquash Workflow

A clean workflow for fixing previous commits:

```bash
# You realize commit abc1234 has a bug. Create a fixup commit:
git commit --fixup=abc1234

# Later, squash all fixup commits into their targets:
git rebase -i --autosquash main
# Git automatically marks fixup commits with "fixup" action
```

---

## Remote Repositories

### Managing Remotes

```bash
# View remotes
git remote -v

# Add a remote
git remote add origin https://github.com/user/repo.git

# Add a secondary remote (e.g., a fork)
git remote add upstream https://github.com/original/repo.git

# Rename a remote
git remote rename origin github

# Remove a remote
git remote remove upstream

# Change a remote's URL (e.g., switch from HTTPS to SSH)
git remote set-url origin git@github.com:user/repo.git

# Show detailed info about a remote
git remote show origin
```

### Fetch, Pull, Push

**Fetch** downloads changes but doesn't modify your working directory:

```bash
# Fetch from default remote (origin)
git fetch

# Fetch from a specific remote
git fetch upstream

# Fetch a specific branch
git fetch origin main

# Fetch all remotes
git fetch --all

# Fetch and prune deleted remote branches
git fetch --prune
```

**Pull** is fetch + merge (or fetch + rebase if configured):

```bash
# Pull current branch
git pull

# Pull with rebase instead of merge (cleaner history)
git pull --rebase

# Pull a specific branch
git pull origin main

# Pull and auto-stash local changes (useful when you have uncommitted work)
git pull --autostash
```

**Push** uploads your commits:

```bash
# Push current branch
git push

# Push and set upstream tracking
git push -u origin feature/user-auth

# Push all branches
git push --all

# Push tags
git push --tags

# Force push (DANGEROUS — overwrites remote history)
git push --force

# Force push with lease (safer — fails if someone else pushed)
git push --force-with-lease

# Push to a different remote branch name
git push origin local-branch:remote-branch
```

> **Always use `--force-with-lease` instead of `--force`**. It checks that the remote hasn't been updated by someone else since your last fetch. If it has, the push fails, preventing you from overwriting their work.

### Tracking Branches

```bash
# Set upstream for current branch
git branch --set-upstream-to=origin/main

# See tracking relationships
git branch -vv

# Output:
#   feature/auth  abc1234 [origin/feature/auth: ahead 2] Add JWT support
#   main          def5678 [origin/main] Update README
# * develop       ghi9012 [origin/develop: behind 3] Refactor API
```

`ahead 2` means you have 2 local commits not yet pushed. `behind 3` means the remote has 3 commits you haven't pulled.

### Working with Forks

```bash
# 1. Fork the repo on GitHub, then clone your fork
git clone https://github.com/your-user/repo.git

# 2. Add the original repo as "upstream"
git remote add upstream https://github.com/original/repo.git

# 3. Keep your fork in sync
git fetch upstream
git switch main
git merge upstream/main
git push origin main

# 4. Create feature branches from the synced main
git switch -c feature/my-contribution
```

---

## Stashing

Stash saves your uncommitted changes temporarily — like putting your work in a drawer so you can come back to it later.

### Basic Stash Operations

```bash
# Stash current changes (tracked files only)
git stash

# Stash with a descriptive message
git stash push -m "WIP: user authentication halfway done"

# Stash including untracked files
git stash -u

# Stash everything, even ignored files
git stash -a

# Stash only specific files
git stash push -m "Stash only config changes" src/config.js src/env.js

# Stash interactively (pick which hunks to stash)
git stash -p
```

### Retrieving Stashed Changes

```bash
# Apply the most recent stash (keeps it in stash list)
git stash apply

# Apply and remove from stash list
git stash pop

# Apply a specific stash
git stash apply stash@{2}

# Pop a specific stash
git stash pop stash@{1}
```

### Managing Stashes

```bash
# List all stashes
git stash list
# Output:
# stash@{0}: On feature/auth: WIP user authentication halfway done
# stash@{1}: On main: Quick fix attempt
# stash@{2}: WIP on develop: abc1234 Add API routes

# Show what's in a stash (as a diff)
git stash show
git stash show -p              # full diff
git stash show stash@{2} -p   # specific stash, full diff

# Drop a specific stash
git stash drop stash@{1}

# Clear ALL stashes (irreversible)
git stash clear

# Create a branch from a stash (useful when stash conflicts with current state)
git stash branch new-branch-name stash@{0}
```

### Practical Stash Scenarios

**Scenario: You need to switch branches but have uncommitted work:**

```bash
git stash push -m "WIP: halfway through login form"
git switch main
# ... do hotfix work ...
git switch feature/login
git stash pop
```

**Scenario: You accidentally started work on the wrong branch:**

```bash
# You're on main but should be on feature/api
git stash
git switch feature/api
git stash pop
```

---

## Logging and History

### Basic Log

```bash
# Standard log
git log

# One-line format (compact)
git log --oneline

# Show the last 5 commits
git log -5

# Log with diff
git log -p

# Log with stats (files changed, insertions, deletions)
git log --stat

# Log with short stats
git log --shortstat
```

### Formatting the Log

```bash
# Graph view with branch visualization
git log --oneline --graph --all --decorate

# Custom format
git log --pretty=format:"%h %ad | %s%d [%an]" --date=short

# Format placeholders:
# %h  = short commit hash
# %H  = full commit hash
# %an = author name
# %ae = author email
# %ad = author date
# %s  = subject (first line of message)
# %b  = body
# %d  = ref names (branches, tags)
```

Example output of `git log --oneline --graph --all`:

```text
* 3a1f2b9 (HEAD -> feature/auth) Add JWT validation
* c4d5e6f Implement login endpoint
| * 8f9a0b1 (main) Update README
|/
* 1a2b3c4 Initial project setup
```

### Filtering the Log

```bash
# Commits by author
git log --author="Sofyan"

# Commits in a date range
git log --after="2026-01-01" --before="2026-06-01"

# Commits containing a string in the message
git log --grep="authentication"

# Commits that changed a specific file
git log -- src/auth.js

# Commits that added or removed a specific string in code
git log -S "API_KEY"      # pickaxe search

# Commits that match a regex in code changes
git log -G "function\s+auth"

# Commits between two refs
git log main..feature/auth     # commits in feature/auth not in main
git log main...feature/auth    # commits in either but not both

# Commits on branch A that aren't on branch B
git log feature/auth --not main
```

### Blame

Shows who last modified each line of a file:

```bash
# Blame a file
git blame src/auth.js

# Blame specific lines
git blame -L 10,20 src/auth.js

# Ignore whitespace changes
git blame -w src/auth.js

# Show the commit that moved or copied lines (detect code movement)
git blame -M src/auth.js

# Show commits that moved lines from other files
git blame -C src/auth.js
```

Output:

```text
a1b2c3d4 (Sofyan  2026-03-15 10:30:00 +0700  1) const express = require('express');
e5f6g7h8 (Partner 2026-04-01 14:22:00 +0700  2) const jwt = require('jsonwebtoken');
a1b2c3d4 (Sofyan  2026-03-15 10:30:00 +0700  3) const router = express.Router();
```

### Show

```bash
# Show a specific commit (diff + metadata)
git show abc1234

# Show only the files changed in a commit
git show --stat abc1234

# Show a file at a specific commit
git show abc1234:src/auth.js

# Show the commit a tag points to
git show v2.0.0
```

---

## Undoing Things

This is where most people panic. Here's a clear guide to what's destructive and what's safe.

### Amend the Last Commit

```bash
# Fix the last commit message
git commit --amend -m "Better commit message"

# Add forgotten files to the last commit
git add forgotten-file.js
git commit --amend --no-edit

# Change author of last commit
git commit --amend --author="Correct Name <correct@email.com>"
```

> **Note**: `--amend` replaces the last commit with a new one (new hash). Only amend commits that haven't been pushed, or be prepared to force-push.

### Unstage Files

```bash
# Unstage a specific file (keep changes in working directory)
git restore --staged src/auth.js

# Unstage everything
git restore --staged .

# Older syntax (still works)
git reset HEAD src/auth.js
```

### Discard Working Directory Changes

```bash
# Discard changes in a specific file (DESTRUCTIVE — changes are gone)
git restore src/auth.js

# Discard all changes in working directory
git restore .

# Older syntax
git checkout -- src/auth.js
```

### Git Reset

Reset moves the branch pointer. Three modes that control what happens to staging and working directory:

```text
                     Branch Pointer   Staging Area   Working Directory
git reset --soft         Moves         Unchanged       Unchanged
git reset --mixed        Moves          Reset          Unchanged
git reset --hard         Moves          Reset            Reset
```

#### Soft Reset

Moves the branch pointer but keeps everything staged. Perfect for "uncommitting" to re-do the commit.

```bash
# Before:
# A --- B --- C (HEAD)

git reset --soft HEAD~1

# After:
# A --- B (HEAD)
# C's changes are staged, ready to re-commit

# Use case: squash last 3 commits into one
git reset --soft HEAD~3
git commit -m "Combined feature: user auth with validation"
```

#### Mixed Reset (Default)

Moves the pointer and unstages changes, but keeps files in working directory.

```bash
# Before:
# A --- B --- C (HEAD)

git reset HEAD~1        # --mixed is the default

# After:
# A --- B (HEAD)
# C's changes are in working directory, unstaged

# Use case: committed too early, want to re-organize what gets staged
git reset HEAD~1
git add src/model.js
git commit -m "Add user model"
git add src/controller.js
git commit -m "Add user controller"
```

#### Hard Reset (Destructive!)

Moves the pointer AND throws away all changes.

```bash
# Before:
# A --- B --- C (HEAD)
# + uncommitted changes in working directory

git reset --hard HEAD~1

# After:
# A --- B (HEAD)
# C is gone. Uncommitted changes are gone. Everything matches commit B.

# Use case: completely discard recent work
git reset --hard HEAD~3

# Reset to match the remote exactly
git reset --hard origin/main
```

> **Warning**: `--hard` is the only reset that can lose uncommitted work. Committed changes can still be recovered via reflog.

### Git Revert

Revert creates a **new commit** that undoes a previous commit. It's safe for shared branches because it doesn't rewrite history.

```bash
# Before:
# A --- B --- C (HEAD)

git revert C

# After:
# A --- B --- C --- C' (HEAD)
# C' undoes everything C did

# Revert a specific commit
git revert abc1234

# Revert without committing (just stage the reversal)
git revert --no-commit abc1234

# Revert a merge commit (must specify which parent to keep)
git revert -m 1 merge-commit-hash
# -m 1 means keep the first parent (usually the main branch)
```

### Reset vs Revert

| | `git reset` | `git revert` |
|---|---|---|
| History | Rewrites (removes commits) | Preserves (adds new commit) |
| Safe for shared branches | No | Yes |
| Can lose work | `--hard` can | No |
| Use case | Local cleanup | Undo on shared branches |

### Reflog — Your Safety Net

Git records every time HEAD moves. Even after a hard reset, you can recover commits using the reflog.

```bash
# Show reflog
git reflog

# Output:
# abc1234 HEAD@{0}: reset: moving to HEAD~3
# def5678 HEAD@{1}: commit: Add user validation
# ghi9012 HEAD@{2}: commit: Add user controller
# jkl3456 HEAD@{3}: commit: Add user model

# Recover! Go back to the state before the reset
git reset --hard HEAD@{1}

# Or create a branch from the lost commit
git branch recovered-work def5678
```

> **Reflog entries expire** after 90 days (default for reachable commits) or 30 days (for unreachable commits). Don't wait too long to recover.

### Restore Specific Files from History

```bash
# Restore a file from a specific commit
git restore --source=abc1234 src/auth.js

# Restore a file from 3 commits ago
git restore --source=HEAD~3 src/auth.js

# Restore a deleted file
git log --diff-filter=D -- path/to/deleted-file.js   # find the commit that deleted it
git restore --source=abc1234^ path/to/deleted-file.js  # restore from commit BEFORE deletion
```

---

## Cherry-pick

Cherry-pick takes a specific commit from one branch and applies it to another. It creates a **new commit** with the same changes but a different hash.

```bash
# Apply a specific commit to current branch
git cherry-pick abc1234

# Cherry-pick without committing (stage the changes only)
git cherry-pick --no-commit abc1234

# Cherry-pick multiple commits
git cherry-pick abc1234 def5678

# Cherry-pick a range of commits (exclusive start, inclusive end)
git cherry-pick abc1234..ghi9012

# Cherry-pick a range (inclusive both ends)
git cherry-pick abc1234^..ghi9012
```

### Cherry-pick Conflict Resolution

```bash
# If there's a conflict during cherry-pick:
# 1. Fix the conflict in the file
# 2. Stage the fixed file
git add src/fixed-file.js

# 3. Continue
git cherry-pick --continue

# Or abort
git cherry-pick --abort
```

### When to Use Cherry-pick

```bash
# Apply a hotfix from main to a release branch
git switch release/v2.0
git cherry-pick hotfix-commit-hash

# Grab a specific feature commit without merging the whole branch
git switch main
git cherry-pick feature-branch-commit-hash
```

> **Caution**: Cherry-picked commits create duplicates (same changes, different hashes). If you later merge the source branch, Git usually handles this fine, but it can cause confusion.

---

## Tags

Tags mark specific points in history — typically releases.

### Lightweight Tags

Just a pointer to a commit (like a branch that doesn't move):

```bash
# Create a lightweight tag
git tag v1.0.0

# Tag a specific commit
git tag v0.9.0 abc1234
```

### Annotated Tags (Recommended)

Store metadata: tagger name, date, and message. These are full Git objects.

```bash
# Create an annotated tag
git tag -a v2.0.0 -m "Release version 2.0.0 — OAuth2 support"

# Tag a specific commit
git tag -a v1.5.0 -m "Patch release for login bug" abc1234
```

### Managing Tags

```bash
# List all tags
git tag

# List tags matching a pattern
git tag -l "v2.*"

# Show tag details
git show v2.0.0

# Delete a local tag
git tag -d v1.0.0

# Delete a remote tag
git push origin --delete v1.0.0

# Push a specific tag to remote
git push origin v2.0.0

# Push all tags
git push origin --tags

# Push only annotated tags (skip lightweight)
git push origin --follow-tags

# Checkout a tag (detached HEAD)
git checkout v2.0.0

# Create a branch from a tag
git switch -c hotfix/v2.0.1 v2.0.0
```

### Semantic Versioning Tags

```text
v1.0.0  →  MAJOR.MINOR.PATCH
  │  │  │
  │  │  └── Bug fixes (backward compatible)
  │  └───── New features (backward compatible)
  └──────── Breaking changes
```

---

## Git Diff

### Comparing Changes

```bash
# Changes in working directory (not yet staged)
git diff

# Changes that are staged (will go into next commit)
git diff --staged
git diff --cached       # same thing

# All changes (staged + unstaged) vs last commit
git diff HEAD

# Diff a specific file
git diff src/auth.js
git diff --staged src/auth.js
```

### Comparing Branches and Commits

```bash
# Diff between two branches
git diff main..feature/auth

# Diff only file names between branches
git diff --name-only main..feature/auth

# Diff with stats
git diff --stat main..feature/auth

# Diff between two commits
git diff abc1234 def5678

# Diff between a commit and HEAD
git diff abc1234 HEAD

# Changes introduced by a specific commit
git diff abc1234^..abc1234
# Or simply:
git show abc1234
```

### Diff Options

```bash
# Ignore whitespace changes
git diff -w

# Ignore changes in amount of whitespace
git diff -b

# Show word-level diff instead of line-level
git diff --word-diff

# Show only the names of changed files
git diff --name-only

# Show names and status (Added, Modified, Deleted)
git diff --name-status

# Generate a diff suitable for applying as a patch
git diff > my-changes.patch
git apply my-changes.patch
```

---

## .gitignore Patterns

The `.gitignore` file tells Git which files to ignore. Patterns are matched against file paths relative to the `.gitignore` location.

### Pattern Syntax

```gitignore
# This is a comment

# Ignore a specific file
secrets.env

# Ignore all files with an extension
*.log
*.tmp

# Ignore a directory (trailing slash)
node_modules/
dist/
build/

# Ignore files in any subdirectory
**/*.pyc

# Ignore files only in the root directory
/TODO.md

# Negate a pattern (un-ignore)
*.log
!important.log

# Ignore everything in a directory except specific files
docs/*
!docs/README.md

# Ignore files by directory depth
# Only in direct subdirectories
*/temp/
# In any level of subdirectories
**/temp/
```

### Common .gitignore Examples

```gitignore
# === Dependencies ===
node_modules/
vendor/
.venv/
__pycache__/

# === Build outputs ===
dist/
build/
*.o
*.pyc

# === IDE / Editor ===
.idea/
.vscode/
*.swp
*.swo
*~
.DS_Store

# === Environment ===
.env
.env.local
.env.*.local

# === Logs ===
*.log
logs/

# === OS files ===
.DS_Store
Thumbs.db

# === Testing ===
coverage/
.nyc_output/

# === Secrets (NEVER commit these) ===
*.pem
*.key
*.p12
credentials.json
```

### Managing Already-Tracked Files

```bash
# Stop tracking a file but keep it on disk
git rm --cached secrets.env
# Then add secrets.env to .gitignore

# Stop tracking an entire directory
git rm -r --cached node_modules/

# After modifying .gitignore, remove all cached tracking and re-add
git rm -r --cached .
git add .
git commit -m "Apply updated .gitignore rules"
```

### Global .gitignore

For files you never want tracked in *any* repository:

```bash
# Create a global gitignore
echo ".DS_Store\n*.swp\n.idea/" >> ~/.gitignore_global

# Tell Git to use it
git config --global core.excludesfile ~/.gitignore_global
```

### Check If a File Is Ignored

```bash
# Check why a file is ignored
git check-ignore -v debug.log
# Output: .gitignore:3:*.log    debug.log

# List all ignored files
git status --ignored
```

---

## Worktrees

Worktrees let you check out multiple branches **simultaneously** in separate directories. No more stashing or committing WIP just to switch branches.

### Basic Worktree Usage

```bash
# Create a worktree for a branch
git worktree add ../hotfix-login hotfix/login-bug

# Now you have:
# /project           → main branch (original)
# /hotfix-login      → hotfix/login-bug branch

# Create a worktree with a new branch
git worktree add -b feature/new-api ../new-api main
```

### Managing Worktrees

```bash
# List all worktrees
git worktree list
# Output:
# /Users/sofyan/project          abc1234 [main]
# /Users/sofyan/hotfix-login     def5678 [hotfix/login-bug]

# Remove a worktree (after you're done)
git worktree remove ../hotfix-login

# Prune stale worktrees (if directory was manually deleted)
git worktree prune
```

### Practical Worktree Scenario

```bash
# You're deep into a feature, and a production bug is reported

# Instead of stashing and switching:
git worktree add ../emergency-fix -b hotfix/prod-crash main

# Fix the bug in the other directory
cd ../emergency-fix
# ... make fixes ...
git add .
git commit -m "Fix null pointer in payment processing"
git push origin hotfix/prod-crash

# Go back to your feature work — no stashing, no context loss
cd ../project
# Your feature branch is exactly where you left it

# Clean up when done
git worktree remove ../emergency-fix
```

---

## Bisect

`git bisect` performs a binary search through your commit history to find exactly which commit introduced a bug. Instead of checking every commit, it cuts the search space in half each time.

### Basic Bisect

```bash
# Start bisecting
git bisect start

# Mark the current commit as bad (has the bug)
git bisect bad

# Mark a known good commit (didn't have the bug)
git bisect good v1.0.0

# Git checks out a commit in the middle. Test it, then:
git bisect good    # if this commit doesn't have the bug
# or
git bisect bad     # if this commit has the bug

# Git keeps narrowing down. Eventually:
# abc1234 is the first bad commit
# commit abc1234
# Author: Someone
# Date: ...
# Message: Refactor payment module

# When done, reset to your original state
git bisect reset
```

### Automated Bisect

If you have a test script that exits 0 for good and non-zero for bad:

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.0.0

# Run automatically — Git tests each commit with your script
git bisect run npm test

# Or with a custom script
git bisect run ./test-login.sh

# Git finds the exact commit automatically
```

### Bisect with a Range

```bash
# If you know the bug was introduced between two commits
git bisect start HEAD abc1234
# equivalent to:
# git bisect start
# git bisect bad HEAD
# git bisect good abc1234
```

### Practical Example

```bash
# Login is broken. It worked in last week's release (v2.3.0).
git bisect start
git bisect bad HEAD
git bisect good v2.3.0

# Git says: "Bisecting: 23 revisions left to test (roughly 5 steps)"
# Git checks out a commit. You test:
npm test
# Tests fail → git bisect bad
# Tests pass → git bisect good

# After ~5 steps:
# "abc1234 is the first bad commit"
# You found it! Now you know exactly what broke it.

git bisect reset
git show abc1234   # inspect the guilty commit
```

---

## Practical Workflows

### Feature Branch Workflow

The most common workflow for teams:

```bash
# 1. Start from an up-to-date main
git switch main
git pull

# 2. Create a feature branch
git switch -c feature/user-dashboard

# 3. Work on the feature (multiple commits are fine)
git add .
git commit -m "Add dashboard layout"
# ... more work ...
git add .
git commit -m "Add charts component"
# ... more work ...
git add .
git commit -m "Connect dashboard to API"

# 4. Keep your branch up to date with main
git fetch origin
git rebase origin/main
# (resolve any conflicts)

# 5. Clean up history before opening a PR
git rebase -i origin/main
# Squash "fix" commits, reword messages

# 6. Push for review
git push -u origin feature/user-dashboard

# 7. After PR is merged, clean up
git switch main
git pull
git branch -d feature/user-dashboard
```

### Hotfix Workflow

```bash
# 1. Branch from main (or the production tag)
git switch main
git pull
git switch -c hotfix/payment-crash

# 2. Fix the bug
git add .
git commit -m "Fix null check in payment processing"

# 3. Push and create PR
git push -u origin hotfix/payment-crash

# 4. After merge, tag the release
git switch main
git pull
git tag -a v2.3.1 -m "Hotfix: payment crash"
git push origin v2.3.1

# 5. Clean up
git branch -d hotfix/payment-crash
git push origin --delete hotfix/payment-crash
```

### Cleaning Up Before a PR

```bash
# You've been working and have messy commits:
# "Add feature"
# "fix typo"
# "WIP"
# "actually fix it"
# "review feedback"

# Squash everything into clean, logical commits:
git rebase -i origin/main

# In the editor:
pick a1b2c3d Add user notification system
fixup e4f5g6h fix typo
fixup i7j8k9l WIP
fixup m0n1o2p actually fix it
fixup q3r4s5t review feedback

# Result: one clean commit "Add user notification system"
```

### Syncing a Fork

```bash
# Add upstream if not already added
git remote add upstream https://github.com/original/repo.git

# Fetch and merge upstream changes
git fetch upstream
git switch main
git merge upstream/main
git push origin main

# Rebase your feature branch on the updated main
git switch feature/my-work
git rebase main
```

### Recovering from Common Mistakes

**Committed to the wrong branch:**

```bash
# Undo the commit but keep changes
git reset --soft HEAD~1

# Switch to the correct branch
git stash
git switch correct-branch
git stash pop
git add .
git commit -m "Your commit message"
```

**Need to undo a pushed commit on a shared branch:**

```bash
# Use revert (safe, doesn't rewrite history)
git revert abc1234
git push
```

**Accidentally deleted a branch:**

```bash
# Find the last commit hash
git reflog

# Recreate the branch
git branch recovered-branch abc1234
```

**Accidentally ran `git reset --hard`:**

```bash
# Don't panic — reflog has your back
git reflog
# Find the commit hash before the reset
git reset --hard HEAD@{1}
```

**Merge went wrong, want to undo it:**

```bash
# If the merge commit hasn't been pushed
git reset --hard HEAD~1

# If the merge commit has been pushed
git revert -m 1 merge-commit-hash
git push
```

### Splitting a Commit

If a commit contains changes that should be separate:

```bash
# Interactive rebase, mark the commit with 'edit'
git rebase -i HEAD~3

# Change 'pick' to 'edit' for the commit to split

# Git stops at that commit. Undo it but keep changes:
git reset HEAD~1

# Now stage and commit separately:
git add src/model.js
git commit -m "Add user model"

git add src/controller.js
git commit -m "Add user controller"

# Continue the rebase
git rebase --continue
```

### Keeping a Clean Working Directory

```bash
# Remove untracked files (dry run first)
git clean -n

# Remove untracked files (actually do it)
git clean -f

# Remove untracked files AND directories
git clean -fd

# Remove untracked AND ignored files (nuclear option)
git clean -fdx

# Interactive clean
git clean -i
```

---

## Mini Cheat Table

### Daily Commands

| Command | What It Does |
|---------|-------------|
| `git status -sb` | Short status with branch info |
| `git add -p` | Stage changes interactively |
| `git commit -m "msg"` | Commit with message |
| `git commit --amend --no-edit` | Add to last commit |
| `git pull --rebase` | Pull with rebase |
| `git push` | Push current branch |
| `git switch -c branch` | Create and switch branch |
| `git switch -` | Switch to previous branch |
| `git stash` | Stash changes |
| `git stash pop` | Restore stashed changes |
| `git log --oneline -10` | Last 10 commits, compact |
| `git diff --staged` | View staged changes |

### Branching

| Command | What It Does |
|---------|-------------|
| `git branch` | List local branches |
| `git branch -a` | List all branches |
| `git switch -c name` | Create and switch |
| `git branch -d name` | Delete merged branch |
| `git branch -D name` | Force delete branch |
| `git branch -m new` | Rename current branch |
| `git push origin --delete name` | Delete remote branch |

### Undoing

| Command | What It Does |
|---------|-------------|
| `git restore file` | Discard working changes |
| `git restore --staged file` | Unstage a file |
| `git reset --soft HEAD~1` | Undo commit, keep staged |
| `git reset HEAD~1` | Undo commit, keep changes |
| `git reset --hard HEAD~1` | Undo commit, discard all |
| `git revert hash` | Undo commit with new commit |
| `git reflog` | Show HEAD movement history |
| `git cherry-pick hash` | Apply specific commit |

### History

| Command | What It Does |
|---------|-------------|
| `git log --oneline --graph --all` | Visual branch graph |
| `git log --author="name"` | Filter by author |
| `git log -S "string"` | Search code changes |
| `git log -- file` | History of a file |
| `git blame file` | Who changed each line |
| `git show hash` | Show commit details |
| `git diff A..B` | Compare two branches |

### Remote

| Command | What It Does |
|---------|-------------|
| `git remote -v` | List remotes |
| `git fetch --prune` | Fetch and clean up |
| `git pull --rebase` | Pull with rebase |
| `git push -u origin branch` | Push with tracking |
| `git push --force-with-lease` | Safe force push |

### Advanced

| Command | What It Does |
|---------|-------------|
| `git rebase -i HEAD~n` | Interactive rebase last n |
| `git bisect start` | Binary search for bugs |
| `git worktree add path branch` | Multiple checkouts |
| `git stash -u` | Stash with untracked |
| `git clean -fd` | Remove untracked files |
| `git tag -a v1.0 -m "msg"` | Create annotated tag |
| `git commit --fixup=hash` | Create fixup commit |
| `git rebase --autosquash` | Auto-apply fixups |

---

*This cheatsheet covers the Git commands you'll use 99% of the time. Master these and you'll handle any workflow thrown at you.*
