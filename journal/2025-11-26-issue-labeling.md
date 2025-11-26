# Issue Labeling Session - 2025-11-26

## Objective
Apply enhanced labeling system (from Issue #874) to gptme repository open issues.

## Work Completed

Applied enhanced labels to 41 issues systematically:

### Label Distribution
- **Difficulty**: 12 easy, 20 medium, 9 hard
- **Status**: 25 ready, 13 needs-design, 2 in-progress, 1 blocked
- **Priority**: 10 low, 24 medium, 7 high
- **Work-Type**: 28 autonomous-friendly, 13 needs-human-judgement

### Issues Labeled

**High Priority Issues:**
- #523: Backtrack conversation on errors (hard, needs-design, high, needs-human-judgement)
- #492: Search is broken (medium, ready, high, autonomous-friendly)
- #458: Anthropic prompt too long (medium, ready, high, autonomous-friendly)
- #443: Browser tool crashes (medium, ready, high, autonomous-friendly)
- #408: Shell tool unreliability (medium, ready, high, autonomous-friendly)
- #216: Computer use support (hard, needs-design, high, needs-human-judgement)
- #59: Add RAG (hard, needs-design, high, needs-human-judgement)

**Medium Priority Issues:**
- #607: ToolSpec refactor (hard, needs-design, medium, needs-human-judgement)
- #576: Background commands (medium, ready, medium, autonomous-friendly)
- #554: Better subagents (hard, needs-design, medium, needs-human-judgement)
- #516: MCP resources (hard, needs-design, medium, needs-human-judgement)
- #495: Tree search (hard, needs-design, medium, needs-human-judgement)
- #478: Improve GitHub bot (medium, needs-design, medium, autonomous-friendly)
- And 18 more medium priority issues...

**Low Priority Issues:**
- #591: Multiple-options suggest (medium, ready, low, autonomous-friendly)
- #569: Queue prompts in CLI (medium, ready, low, autonomous-friendly)
- #413: Fix rich print output (easy, ready, low, autonomous-friendly)
- And 7 more low priority issues...

### Judgment Calls Made

Following Erik's note that inconsistent application is expected:

1. **Difficulty Assessment**: Based on estimated time and complexity
   - Easy: <4 hours, straightforward implementation
   - Medium: 4-8 hours, moderate complexity
   - Hard: >8 hours, architectural changes

2. **Status Selection**:
   - "ready" when problem and solution clear
   - "needs-design" when approach needs discussion
   - "in-progress" for partially completed work
   - "blocked" when dependent on other issues

3. **Priority Levels**:
   - High: Affects reliability or core functionality
   - Medium: Valuable enhancements or moderate bugs
   - Low: Nice-to-have features or edge cases

4. **Work-Type Classification**:
   - "autonomous-friendly": Clear scope, good for agent work
   - "needs-human-judgement": Requires architectural or UX decisions

### Issues Not Yet Labeled

Many issues remain without enhanced labels. Prioritized issues most likely to be worked on soon. This provides good coverage while avoiding over-labeling.

## Insights

1. **Most Common Pattern**: medium difficulty, ready status, autonomous-friendly
2. **High Priority Focus**: Reliability issues and core feature gaps
3. **Design Work Needed**: Many enhancements need architectural decisions (13 issues marked needs-design)
4. **Autonomous Opportunities**: 28 issues marked as autonomous-friendly, good work pipeline

## Next Steps

- Labels now available for filtering and discovery
- Erik can review and adjust as needed
- Contributors can use labels to find appropriate work
- Autonomous agents can filter for autonomous-friendly + ready + appropriate difficulty

## Time Spent

~15 minutes for 41 issues (avg 22 seconds per issue)
