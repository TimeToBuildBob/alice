# Journal Entry Patterns

This guide documents conventions and best practices for journal entries in the workspace.

## Purpose

Journal entries provide a daily log of activities, decisions, and progress. They serve as:
- Historical record of work done
- Context for future sessions
- Decision rationale documentation
- Progress tracking mechanism
- Learning and reflection tool

## File Naming

### Convention

**Format**: `YYYY-MM-DD-topic.md`

**Rules**:
- Always use absolute path: `/home/bob/alice/journal/YYYY-MM-DD-topic.md`
- Date in ISO 8601 format (YYYY-MM-DD)
- Topic describes the main focus/task
- Use hyphens in topic, not spaces or underscores
- Keep topic concise (2-4 words typical)

### Examples

```bash
# ✅ Correct naming
/home/bob/alice/journal/2025-11-19-knowledge-base-development.md
/home/bob/alice/journal/2025-11-19-task-cli-setup.md
/home/bob/alice/journal/2025-11-18-first-autonomous-run.md

# ❌ Wrong - relative path
journal/2025-11-19-topic.md

# ❌ Wrong - wrong date format
/home/bob/alice/journal/11-19-2025-topic.md
/home/bob/alice/journal/2025-11-19.md  # Missing topic

# ❌ Wrong - spaces or underscores
/home/bob/alice/journal/2025-11-19 topic name.md
/home/bob/alice/journal/2025-11-19_topic_name.md
```

## When to Create New Files

### NEW Topic = NEW File

**Create new file when**:
- Starting a new distinct task or project
- Different focus from previous journal entries
- New session with different goal
- Want to keep topics separate for reference

**Examples**:
```bash
# Different tasks = different files
journal/2025-11-19-git-remote-setup.md      # Git setup
journal/2025-11-19-task-cli-setup.md        # Task CLI setup
journal/2025-11-19-knowledge-base-development.md  # Documentation

# Same date, different topics
journal/2025-11-19-morning-standup.md       # Morning planning
journal/2025-11-19-pr-review.md            # Afternoon PR work
```

### Append to Existing File

**Append when**:
- Continuing work on same task/topic
- Adding updates to ongoing work
- Multiple sessions on same project
- Incremental progress on same focus

**Example**:
```bash
# Morning: Create file
save /home/bob/alice/journal/2025-11-19-feature-implementation.md

# Afternoon: Append progress
append /home/bob/alice/journal/2025-11-19-feature-implementation.md
## Afternoon Update
Made progress on...
```

## Entry Structure

### Standard Template

```markdown
# Title (Task or Topic Name)

**Date**: YYYY-MM-DD
**Time**: HH:MM-HH:MM UTC
**Session**: Brief description
**Task**: Link to task file if applicable

## Overview

Brief summary of what was accomplished or attempted.

## Work Completed

### ✅ Specific Item 1
Details about what was done...

### ✅ Specific Item 2
More details...

## Technical Notes

Any technical observations, issues encountered, solutions found.

## Session Summary

Duration: X minutes
Outcome: ✅ SUCCESS / ⚠️ PARTIAL / ❌ BLOCKED

Key achievements and next steps.

---

*This entry committed with session completion.*
```

### Flexible Sections

Adapt sections based on session type:

**For implementation work**:
- Work Completed
- Technical Notes
- Code Changes
- Testing Results
- Next Steps

**For research/investigation**:
- Questions Explored
- Findings
- Resources Consulted
- Conclusions
- Open Questions

**For documentation**:
- Documents Created/Updated
- Patterns Documented
- Examples Added
- Related Work

## Best Practices

### 1. Absolute Paths

**Always** use absolute paths for workspace files in journal entries:

```markdown
# ✅ Correct
Created task file at `/home/bob/alice/tasks/new-task.md`
Updated work queue at `/home/bob/alice/state/queue-manual.md`

# ❌ Wrong
Created task file at `tasks/new-task.md`
```

### 2. Link to Resources

Link to referenced resources:

```markdown
## Related Work

- Task: tasks/implement-feature.md
- Knowledge: knowledge/pattern-guide.md
- Person: people/collaborator.md
- External: [GitHub PR #123](https://github.com/owner/repo/pull/123)
```

### 3. Document Decisions

Record the rationale behind decisions:

```markdown
## Decision: Tool Selection

Chose to use `patch` instead of `save` because:
- File is large (500 lines)
- Only changing specific function
- Want to preserve comments
- Safer for targeted edits
```

### 4. Track Progress

Use checkboxes for subtasks:

```markdown
## Progress on Feature

- [x] Write initial implementation
- [x] Add unit tests
- [ ] Integration testing
- [ ] Documentation
```

### 5. Note Blockers

Document what prevented completion:

```markdown
## Blockers

- External API rate limit hit (resume after 1 hour)
- Need clarification on requirements (asked creator)
- Dependency not yet available (tracked in issue #45)
```

### 6. Session Metadata

Always include timing and outcome:

```markdown
**Date**: 2025-11-19
**Time**: 14:00-14:30 UTC
**Duration**: 30 minutes
**Outcome**: ✅ SUCCESS
```

## Common Patterns

### Autonomous Session Entry

```markdown
# Autonomous Session YYYYMMDD-HHMM

**Date**: YYYY-MM-DD
**Time**: HH:MM UTC
**Trigger**: Scheduled/Manual/Event
**Task**: Selected task description

## CASCADE Task Selection

1. PRIMARY: Checked queue-manual.md - Found X
2. SECONDARY: Checked notifications - Found Y
3. Selected: Task Z

## Work Completed

[Details of work...]

## Session Summary

Duration: X minutes
Outcome: ✅ SUCCESS

---

*Session completed and documented.*
```

### Multi-Session Project

**Day 1**:
```markdown
# Project Setup

**Date**: 2025-11-19

## Day 1: Initial Setup

[Setup work...]

---

*Session 1 complete*
```

**Day 2** (append):
```markdown
## Day 2: Implementation Progress

**Time**: 10:00-11:00 UTC

[Implementation work...]

---

*Session 2 complete*
```

### Quick Update Entry

```markdown
# Quick Update: Bug Fix

**Date**: 2025-11-19
**Time**: 15:30 UTC

Fixed issue with path resolution in task CLI.

**Change**: Updated `scripts/tasks.py` line 45 to use absolute paths.
**Result**: ✅ Task CLI now works from any directory.

---

*Committed with fix*
```

## Integration with Task System

### Link Journal ↔ Task

**In journal entry**:
```markdown
Working on: tasks/implement-feature.md
```

**In task file**:
```markdown
## Progress

- 2025-11-19: Initial implementation (see journal/2025-11-19-feature-implementation.md)
- 2025-11-20: Testing phase (see journal/2025-11-20-feature-testing.md)
```

## Commit Practices

### When to Commit

- At session completion
- After significant progress
- Before switching topics
- When documenting decisions

### Commit Message

```bash
# Journal entries typically use docs: type
git commit -m "docs: session YYYYMMDD-HHMM - brief description"

# Examples
git commit -m "docs: session 20251119-1400 - feature implementation progress"
git commit -m "docs: autonomous run documentation"
```

## Location Rules

### ✅ Correct Location

Journal entries always go in workspace:
