---
keywords: ["git commit", "git push", "external repo", "worktree", "pull request", "master", "main branch"]
---

# Rule

Never commit directly to master/main on external repositories. Always use worktrees for external repo PRs. Workspace repo allows direct master commits.

# Context

There are two types of repositories you work with:
1. **Workspace repo** (/home/bob/alice) - Your personal workspace
2. **External repos** (projects/, cloned repos) - Collaborative projects

These require different git workflows to avoid conflicts and maintain collaboration hygiene.

# Detection

You're working with external repo when:
- Repository is in projects/ directory
- Repository is a clone of someone else's project
- Repository has active collaboration (multiple contributors)
- You're creating or updating a pull request

You're working with workspace repo when:
- Repository is /home/bob/alice
- It's your personal workspace/notes
- No external collaboration on this branch

# Pattern

**External Repositories (MUST use worktrees)**:
```bash
# ✅ Correct - Use worktree for PR work
cd /path/to/external/repo
git worktree add ../repo-pr-branch origin/pr-branch
cd ../repo-pr-branch
# Make changes, commit, push
git push origin pr-branch

# ❌ Wrong - Direct commit to master
cd /path/to/external/repo
git checkout master
git commit -m "fix" # DANGEROUS
git push # Could conflict with others
```

**Workspace Repository (Can commit directly)**:
```bash
# ✅ Correct - Direct commits OK
cd /home/bob/alice
git add journal/2025-11-19.md
git commit -m "docs: session updates"
git push
```

# Workflow Comparison

**External Repo Workflow**:
1. Create/update PR via GitHub
2. Create worktree for PR branch
3. Make changes in worktree
4. Commit and push to PR branch
5. Never touch master/main directly

**Workspace Repo Workflow**:
1. Make changes directly
2. Commit to master
3. Push to origin
4. Simple and fast

# Outcome

Following these patterns:
- External repos: No conflicts, clean PR workflow, safe collaboration
- Workspace repo: Fast iteration, simple commits, personal control
- Clear distinction: Prevents mistakes from mixing workflows

# Related

- [Autonomous Operation Patterns](../../knowledge/autonomous-operation-patterns.md) - Git workflow section
- [ARCHITECTURE.md](../../ARCHITECTURE.md) - Repository structure
