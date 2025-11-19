# CASCADE Task Selection Workflow

## Overview

CASCADE is a structured methodology for task selection in autonomous sessions, designed to maximize execution time while maintaining alignment with manual priorities and strategic goals.

The name CASCADE reflects the waterfall pattern: check highest priority sources first, falling through to next level only when blocked.

## The Three-Step Workflow

### Step 1: Quick Loose Ends Check (2-5 min max)
**Purpose**: Clear immediate blockers without getting distracted

Actions:
- Check `git status` for uncommitted work
- Scan for critical notifications only
- Fix only immediate blockers

**Anti-pattern**: Spending >5 min investigating issues. If it takes longer, add to queue and continue.

### Step 2: Task Selection via CASCADE (5-10 min max)
**Purpose**: Efficiently select highest-value work

#### PRIMARY Source: Manual Queue
Read `/home/bob/alice/state/queue-manual.md` "Planned Next" section:
- If task available and unblocked → COMMIT and proceed
- If all tasks blocked or missing → Fall to SECONDARY

#### SECONDARY Source: Notifications
Check for direct assignments:
- GitHub mentions/assignments
- Email action items
- Discord pings requiring response

If actionable notification → COMMIT and proceed
If none → Fall to TERTIARY

#### TERTIARY Source: Workspace Tasks
Check `/home/bob/alice/tasks/` for active tasks:
- Read task metadata to find `state: active` or `state: new`
- Select based on priority and dependencies
- If all blocked → Document real blocker situation

**Selection Efficiency Target**: <10 tool calls OR <20k tokens

### Step 3: EXECUTION (20-30 min)
**Purpose**: Make substantial progress on selected task

This is the main work phase:
- Implement, test, verify
- Don't stop early - work until real blocker or token hint
- Document progress in journal
- Commit and push changes

## Real Blocker Criteria

To claim "all tasks blocked", you must have checked ALL three sources:
1. ✅ PRIMARY → All blocked
2. ✅ SECONDARY → Nothing actionable
3. ✅ TERTIARY → All blocked

**If any source has actionable work → You're not blocked**

## Time Allocation

Target distribution for 40min session:
- Step 1: 2-5 min (5-10%)
- Step 2: 5-10 min (12-25%)
- Step 3: 20-30 min (50-75%)
- Completion: 2-5 min (5-10%)

**Key principle**: Minimize selection overhead to maximize execution time.

## Integration with Work Queues

### Manual Queue (queue-manual.md)
- **Maintained by**: Agent during autonomous sessions
- **Purpose**: Strategic priorities, manually curated tasks
- **Update frequency**: Every session (refresh "Planned Next")

### Generated Queue (queue-generated.md)
- **Maintained by**: Automation scripts (future)
- **Purpose**: Tasks from GitHub, email, other sources
- **Fallback**: Use when manual queue empty

## Common Pitfalls

### Over-planning
**Symptom**: Spending >10 min in Step 2
**Fix**: Pick any reasonable task from PRIMARY source and start working

### False Blockers
**Symptom**: Claiming blocked without checking all three sources
**Fix**: Systematically check PRIMARY → SECONDARY → TERTIARY

### Premature Completion
**Symptom**: Finishing Step 3 in <15 min without hitting real blocker
**Fix**: Continue working - most tasks can be advanced further

### Context Churn
**Symptom**: Reading same files multiple times during selection
**Fix**: Read once, commit quickly, start execution

## Success Metrics

Good CASCADE execution shows:
- <10 tool calls in Step 2
- >60% of session time in Step 3
- Clear task commitment before execution
- Verifiable progress on selected task

## Related

- [Autonomous Operation Guide](./autonomous-operation-patterns.md) - Overall autonomous session patterns
- [Token Efficiency](./token-efficiency-patterns.md) - Context optimization techniques
- [Task State Management](./task-state-management.md) - Task lifecycle and metadata
