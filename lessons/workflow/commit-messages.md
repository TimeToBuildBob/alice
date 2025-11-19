---
keywords: ["git commit", "commit message", "git add", "conventional commits"]
---

# Rule

Use Conventional Commits format for all commit messages. Clear type prefix and concise description.

# Context

Consistent commit messages make it easy to:
- Understand what changed at a glance
- Generate changelogs automatically
- Review history efficiently
- Maintain professional standards

The workspace uses Conventional Commits format consistently. Follow this pattern for all commits.

# Detection

You need this when:
- About to commit changes (`git commit -m`)
- Writing commit messages in any repository
- Documenting what changed in a commit

# Pattern

**Format**: `<type>(<scope>): <description>`

**Common types**:
```bash
# ✅ Documentation changes
git commit -m "docs: update work queue after session"
git commit -m "docs: add git workflow lesson"

# ✅ New features
git commit -m "feat: install task management CLI"
git commit -m "feat(alice): configure autonomous operation"

# ✅ Bug fixes
git commit -m "fix: update pyproject.toml format"
git commit -m "fix: use absolute path in script"

# ✅ Refactoring
git commit -m "refactor: simplify session completion"

# ✅ Chores/maintenance
git commit -m "chore: update dependencies"
```

**Scope is optional**:
```bash
# With scope (adds context)
git commit -m "feat(cli): add task filtering"

# Without scope (also valid)
git commit -m "feat: add task filtering"
```

# Anti-pattern

```bash
# ❌ Vague messages
git commit -m "updates"
git commit -m "fix stuff"
git commit -m "wip"

# ❌ No type prefix
git commit -m "update work queue"
git commit -m "add new lesson"

# ❌ Too verbose
git commit -m "docs: this commit updates the work queue file to reflect the completion of the session and adds new planned tasks"
```

# Outcome

Using Conventional Commits:
- Clear commit history
- Easy to understand changes
- Professional standard
- Changelog generation possible
- Quick git log scanning

# Related

- [git-workflow.md](./git-workflow.md) - Repository workflow patterns
- [Autonomous Operation Patterns](../../knowledge/autonomous-operation-patterns.md) - Commit patterns section
