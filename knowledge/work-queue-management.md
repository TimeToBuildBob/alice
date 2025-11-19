# Work Queue Management

## Overview

The work queue system provides strategic direction for autonomous sessions through a two-queue architecture:

- **Manual Queue** (`state/queue-manual.md`) - Human-curated strategic priorities
- **Generated Queue** (`state/queue-generated.md`) - Auto-generated from tasks/GitHub

This guide covers best practices for maintaining effective work queues.

## Queue Philosophy

**Principle**: Queues are planning tools, not execution logs.

- Keep queue lean (3 items in "Planned Next")
- One-line summaries in "Current Run"
- Details belong in journal/knowledge base
- Update every session to reflect reality

## Manual Queue Structure

### Required Sections

```markdown
# Work Queue

## Current Run
Session YYYYMMDD-HHMM: ✅ One-line summary

## Planned Next

1. **Task Title** (progress indicator, state)
   - Priority: HIGH/MEDIUM/LOW
   - Goal: Brief objective
   - Next Action: Specific actionable step
   - Status: Current state or blocker
   - Timeline: Estimated time
   - Source: tasks/file.md or issue #123
   - Note: Optional context

2. **Second Task**
   ...

3. **Third Task** OR **[Available for new priority task]**

## Recently Completed

- ✅ Task (YYYY-MM-DD HH:MM UTC) - Brief note
- ✅ Task (YYYY-MM-DD HH:MM UTC) - Brief note

## Last Updated
YYYY-MM-DD HH:MM UTC
```

### Section Guidelines

#### Current Run
**Purpose**: Quick status check for what's happening right now

**Format**: `Session YYYYMMDD-HHMM: ✅ Brief summary`

**Examples**:
```markdown
✅ Documented CASCADE and session completion workflows
✅ Fixed pyproject.toml deprecation warning
✅ Created initial knowledge base structure
⏸️ Paused due to external blocker
🔄 In progress - implementing feature X
```

**Keep it**:
- One line
- Action-focused
- Timestamped with session ID

#### Planned Next
**Purpose**: Top 3 priorities for upcoming sessions

**Selection Criteria**:
1. Unblocked and actionable
2. High impact or strategic value
3. Clear next action defined

**Format Requirements**:
- Exactly 3 items (or 2 + available slot)
- Specific next action (not vague "work on X")
- Realistic timeline estimates
- Source reference for context

**Good Examples**:
```markdown
1. **Implement Email Watcher** (3/8 subtasks, active)
   - Priority: HIGH
   - Goal: Enable automated email responses
   - Next Action: Write Gmail API integration
   - Status: Foundation complete, API next
   - Timeline: 30-45 min
   - Source: tasks/email-automation.md

2. **Expand Knowledge Base** (untracked, ongoing)
   - Priority: LOW
   - Goal: Document patterns as they emerge
   - Next Action: Create next knowledge article
   - Status: Ongoing - 8 articles complete
   - Timeline: 15-20 min per article
   - Source: Autonomous operations
```

**Bad Examples**:
```markdown
1. **Work on stuff** - Too vague
2. **Fix all bugs** - No specific scope
3. **Make things better** - No actionable next step
```

#### Recently Completed
**Purpose**: Track recent wins and maintain motivation

**Format**: `- ✅ Task (YYYY-MM-DD HH:MM UTC) - Brief note`

**Keep**:
- Last 5-7 completed items
- Remove older items to keep section compact
- Include meaningful completion notes

**Don't Include**:
- Routine maintenance (unless significant)
- Minor documentation updates
- Failed/cancelled attempts

#### Last Updated
**Purpose**: Know when queue was last refreshed

**Format**: `YYYY-MM-DD HH:MM UTC`

**Update**: Every session, even if queue unchanged

## Update Patterns

### During Session Start
1. Check if current task is still valid
2. Remove completed items from "Planned Next"
3. Add newly discovered priorities
4. Reorder by current priority

### During Session End
1. Update "Current Run" with one-line summary
2. Refresh "Planned Next" (3 items)
3. Move completed items to "Recently Completed"
4. Update "Last Updated" timestamp

### When Task Becomes Blocked
1. Update task status with blocker details
2. Move to lower priority or mark explicitly blocked
3. Promote next unblocked task to top

### When New Priority Emerges
1. Add to "Planned Next" in priority order
2. If list full (3 items), move lowest to "Available" note
3. Document why it's a priority

## Priority Levels

### HIGH Priority
- Blocks other work
- Time-sensitive (deadlines, opportunities)
- High impact on goals
- External dependencies waiting

**Example**: User-requested feature with deadline

### MEDIUM Priority
- Important but not urgent
- Improves quality of life
- Routine maintenance with value

**Example**: Refactoring for better maintainability

### LOW Priority
- Nice to have
- Background work
- Ongoing improvements

**Example**: Expanding documentation

## Task States in Queue

### Active
Currently being worked on or ready to start

```markdown
**Task** (progress, active)
- Status: Making progress
```

### Blocked
Cannot proceed without external input

```markdown
**Task** (progress, blocked)
- Status: BLOCKED - Waiting for X
```

### In Progress
Started but paused, can resume

```markdown
**Task** (progress, in progress)
- Status: Paused at checkpoint Y
```

### Available Slot
Placeholder for new priorities

```markdown
**[Available for new priority task]**
```

## Common Pitfalls

### ❌ Queue Bloat
**Symptom**: >5 items in "Planned Next"

**Fix**: Keep exactly 3 items. Move extras to tasks/ with proper metadata.

**Why**: More items = decision paralysis, stale information

### ❌ Stale Information
**Symptom**: Queue hasn't been updated in >2 sessions

**Fix**: Update queue every session, even if unchanged

**Why**: Builds trust that queue reflects reality

### ❌ Vague Next Actions
**Symptom**: "Continue working on X" or "Make progress on Y"

**Fix**: Specify concrete next step: "Write test for Y" or "Deploy X to staging"

**Why**: Specific actions are easier to pick up and execute

### ❌ Over-detailed Status
**Symptom**: Multi-paragraph status updates in queue

**Fix**: One line in queue, details in journal entry

**Why**: Queue is for scanning, journal is for reading

### ❌ Forgetting Source References
**Symptom**: No source link for tasks

**Fix**: Always include `Source: tasks/file.md` or issue URL

**Why**: Need context to understand and execute task

## Integration with Tools

### Task CLI Integration
When available, sync queue with task metadata:

```bash
# List active tasks for queue planning
./scripts/tasks.py status --compact

# Update task state when starting from queue
./scripts/tasks.py edit task-id --set state active
```

### Generated Queue Integration
Future automation will populate `queue-generated.md` from:
- GitHub issues assigned to agent
- Email action items
- Discord mentions
- Scheduled maintenance tasks

Manual queue takes precedence in CASCADE workflow.

## Success Metrics

Good queue management shows:
- Queue updated every session
- All 3 slots filled with actionable tasks
- Specific next actions defined
- Source references included
- Realistic timelines
- Progress indicators current

## Related

- [CASCADE Workflow](./cascade-workflow.md) - Task selection using queues
- [Session Completion](./session-completion.md) - Queue update process
- [Task State Management](./task-state-management.md) - Task metadata and lifecycle
- [Autonomous Operation](./autonomous-operation-patterns.md) - Overall patterns
