# Session Completion Workflow

## Overview

A structured approach to ending autonomous sessions that ensures:
- Work is properly documented
- Queue stays current and actionable
- Changes are committed and pushed
- Next session has clear starting point

**Time budget**: 2-5 minutes maximum

## The Completion Checklist

### 1. Return to Workspace
```bash
cd /home/bob/alice
```

**Why**: Ensures all relative paths work correctly for queue updates and commits.

### 2. Update Work Queue

Edit `/home/bob/alice/state/queue-manual.md`:

**Current Run Section**:
```markdown
## Current Run
Session YYYYMMDD-HHMM: ✅ Brief one-line summary of what was accomplished
```

**Planned Next Section**:
- Review the 3 listed tasks
- Remove completed items
- Add new priorities if discovered
- Reorder by current priority
- Keep exactly 3 tasks listed

**Last Updated**:
```markdown
## Last Updated
YYYY-MM-DD HH:MM UTC
```

**Example**:
```markdown
## Current Run
Session 20251119-1900: ✅ Documented CASCADE and session completion workflows

## Planned Next

1. **Complete Initial Agent Setup** (0/13 subtasks, active)
   - Priority: HIGH
   - Status: BLOCKED - Requires interactive session

2. **Expand Knowledge Base** (untracked, in progress)
   - Priority: LOW
   - Progress: 13 documents (5 lessons + 8 knowledge articles)
   - Next Action: Continue organic expansion

3. **[Available for new priority task]**

## Last Updated
2025-11-19 19:05 UTC
```

### 3. Commit and Push

Use a single atomic commit for all session documentation:

```bash
git add journal/*.md state/queue-manual.md && \
git commit -m "docs: session updates" && \
git push
```

**Why single commit**:
- Keeps history clean
- Groups related changes
- Fast to execute

**Commit message format**:
- Use "docs:" prefix (Conventional Commits)
- Keep it generic ("session updates")
- Details are in the files themselves

### 4. Use Complete Tool

```complete
```

This signals the autonomous session is finished and prevents unnecessary continuation.

## What NOT to Do

### ❌ Verbose Documentation
**Anti-pattern**:
```markdown
## Current Run
Session 20251119-1900: In this session, I documented the CASCADE workflow
which is a three-step methodology for task selection. I also created
documentation for session completion patterns. The CASCADE workflow includes...
```

**Correct**:
```markdown
## Current Run
Session 20251119-1900: ✅ Documented CASCADE and session completion workflows
```

**Why**: Queue reader can look at journal or git log for details. One line is enough.

### ❌ Multiple Commits
**Anti-pattern**:
```bash
git add journal/2025-11-19-cascade.md
git commit -m "docs: add CASCADE journal entry"
git push

git add state/queue-manual.md
git commit -m "docs: update work queue"
git push
```

**Correct**: Single atomic commit (shown above)

**Why**: Faster, cleaner history, atomic operation

### ❌ Forgetting to Push
**Anti-pattern**:
```bash
git commit -m "docs: session updates"
# Oops, forgot to push!
```

**Correct**: Always include `&& git push` in the command

**Why**: Other processes need the updates (next session, monitors, backups)

### ❌ Over-explaining in Queue
**Anti-pattern**: Repeating all work details in "Current Run" section

**Correct**: One-line summary with ✅ checkmark

**Why**: Details are in journal entries, commits, and knowledge base

## Time Management

Target completion time: **2-5 minutes**

If taking longer:
- You're over-documenting
- Keep updates minimal
- Details go in journal/knowledge, not queue

## Integration with Journal System

Journal entries contain the detailed narrative:
- What was attempted
- What worked/didn't work
- Blockers encountered
- Lessons learned
- Next steps

Queue contains only:
- Current session one-liner
- Next 3 priorities
- Timestamps

**Separation of concerns**: Journal = story, Queue = action items

## Verification

Before using `complete` tool, verify:
- ✅ Back in workspace directory (`pwd` shows `/home/bob/alice`)
- ✅ Queue updated with current timestamp
- ✅ Changes committed with `git status` showing clean
- ✅ Changes pushed (check `git status` shows "up to date")

## Example Full Completion

```bash
# 1. Return to workspace
cd /home/bob/alice

# 2. Update queue (edit queue-manual.md in editor or with patch tool)

# 3. Commit and push
git add journal/*.md state/queue-manual.md && \
git commit -m "docs: session updates" && \
git push

# 4. Complete
```

Then use `complete` tool.

## Success Metrics

Good completion shows:
- Single commit with all updates
- Queue timestamp current
- "Planned Next" reflects reality
- Changes pushed successfully
- Took <5 minutes

## Related

- [Autonomous Operation Guide](./autonomous-operation-patterns.md) - Overall session patterns
- [CASCADE Workflow](./cascade-workflow.md) - Task selection methodology
- [Journal Entry Patterns](./journal-entry-patterns.md) - How to structure journal entries
- [Token Efficiency](./token-efficiency-patterns.md) - Keeping documentation concise
