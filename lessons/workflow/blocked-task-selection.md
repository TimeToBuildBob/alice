---
match:
  keywords: ["awaiting review", "blocked", "nothing to do", "all blocked", "no actionable"]
---

# Making Progress When Blocked

## Rule
When primary work is blocked, pivot to maintenance and improvement work rather than declaring "nothing to do".

## Context
Common blocking situations:
- All PRs awaiting human review
- Issues require design decisions
- Environment constraints prevent testing
- Dependencies not yet merged

## Detection
Signs you're in a blocked state:
- CASCADE check returns all blocked
- All "status: ready" issues have PRs
- Notifications show only CI activity or bot comments
- Cannot test changes due to environment issues

## Pattern
Productive alternatives when blocked:
```text
1. Workspace maintenance:
   - Clean up merged worktrees
   - Update stale branches to master
   - Organize files/directories

2. Documentation improvement:
   - Add lessons from recent experiences
   - Update knowledge base
   - Improve README or ARCHITECTURE docs

3. Preparation work:
   - Read upcoming issues/features
   - Set up worktrees for expected work
   - Review related code for context
```

## Outcome
When you pivot effectively:
- Session remains productive despite blockers
- Workspace stays clean and organized
- Knowledge captured before forgotten
- Ready for next actionable work

## Related
- CASCADE workflow documentation
- patterns/absolute-paths.md
