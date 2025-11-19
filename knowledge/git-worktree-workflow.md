# Git Worktree Workflow

## Overview

Git worktrees enable working on multiple branches simultaneously without switching contexts. This is **essential** for contributing to external repositories where:

- MUST use worktrees for PR branches
- MUST NOT commit directly to master/main
- MUST maintain clean separation between branches

## Core Principle

**One directory per branch** - each worktree is a separate working directory linked to the same repository.

## Basic Operations

### List Existing Worktrees
```bash
git worktree list
```

### Create New Worktree
```bash
# Create new branch and worktree
git worktree add ../repo-feature-name -b feature-name

# Create worktree from existing branch
git worktree add ../repo-bugfix-123 bugfix-123
```

**Naming convention**: `../repo-branch-name` keeps worktrees organized alongside main repo

### Work in Worktree
```bash
cd ../repo-feature-name
# Make changes, commit normally
git add .
git commit -m "feat: implement feature"
git push origin feature-name
```

### Remove Worktree
```bash
# From main repo or any worktree
git worktree remove ../repo-feature-name

# Or delete directory and clean up
rm -rf ../repo-feature-name
git worktree prune
```

## PR Workflow for External Repos

### 1. Fork and Clone
```bash
# One-time setup
gh repo fork owner/repo --clone

# Or clone your fork
git clone git@github.com:your-username/repo.git
cd repo
git remote add upstream https://github.com/owner/repo.git
```

### 2. Create PR Branch in Worktree
```bash
# Update fork
git fetch upstream
git checkout main
git merge upstream/main
git push origin main

# Create PR branch in worktree
git worktree add ../repo-fix-bug -b fix-bug
cd ../repo-fix-bug

# Make changes
git add .
git commit -m "fix: resolve bug"
git push origin fix-bug
```

### 3. Create PR
```bash
# From worktree
gh pr create --title "Fix bug" --body "Description"

# Or from main repo
cd ../repo
gh pr create --head your-username:fix-bug
```

### 4. Read PR with Full Context
```bash
# View PR details including review comments and code context
gh pr view <pr_url>

# This shows:
# - PR description
# - Review comments
# - Code context
# - Suggestions
```

### 5. Update PR Based on Review
```bash
# Still in worktree
cd ../repo-fix-bug

# Make requested changes
git add .
git commit -m "fix: address review comments"
git push origin fix-bug

# Comment on PR
gh pr comment <pr_url> --body "Updated based on feedback"
```

### 6. Check CI Status
```bash
# Quick status check with failed run IDs
gh pr status <pr_url>

# Check specific commit (useful for older commits)
gh pr status <pr_url> <commit_sha>

# Wait for CI to complete
gh pr checks <pr_url>

# View failed test logs
gh run view <run_id> --log-failed
```

### 7. Clean Up After Merge
```bash
# Return to main repo
cd ../repo

# Remove worktree
git worktree remove ../repo-fix-bug

# Delete local branch
git branch -d fix-bug

# Update main
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

## Workspace Repo Workflow

For the workspace repository (`/home/bob/alice`), worktrees are NOT required:

```bash
# Can commit directly to master
cd /home/bob/alice
git add .
git commit -m "docs: update knowledge base"
git push
```

**Why different**:
- Workspace is personal repository
- No PR review process
- Direct commits are expected

## Common Pitfalls

### ❌ Committing to Master in External Repos
**Anti-pattern**:
```bash
cd external-repo
git checkout main
# Make changes
git commit -m "feat: new feature"
git push  # ❌ Wrong! Don't push to main
```

**Correct**:
```bash
cd external-repo
git worktree add ../external-repo-feature -b feature
cd ../external-repo-feature
# Make changes
git commit -m "feat: new feature"
git push origin feature
gh pr create
```

### ❌ Forgetting to Update Fork
**Anti-pattern**:
```bash
# Create PR branch from stale main
git worktree add ../repo-feature -b feature
```

**Correct**:
```bash
# Update first
git fetch upstream
git checkout main
git merge upstream/main
git push origin main

# Then create branch
git worktree add ../repo-feature -b feature
```

### ❌ Working in Main Repo Instead of Worktree
**Anti-pattern**:
```bash
# In main repo
git checkout -b feature
# Make changes... now can't work on another PR without stashing
```

**Correct**:
```bash
# Create worktree
git worktree add ../repo-feature -b feature
cd ../repo-feature
# Make changes... main repo still available for other work
```

### ❌ Forgetting to Clean Up Worktrees
**Symptom**: Accumulating old worktree directories

**Fix**:
```bash
# List all worktrees
git worktree list

# Remove old ones
git worktree remove ../repo-old-feature
git worktree prune
```

### ❌ Creating Worktree in Wrong Location
**Anti-pattern**:
```bash
# Creates worktree inside main repo
git worktree add feature -b feature  # Wrong!
```

**Correct**:
```bash
# Creates worktree alongside main repo
git worktree add ../repo-feature -b feature
```

## Directory Structure Example
