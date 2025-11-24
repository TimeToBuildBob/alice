# Issue Labeling Analysis for Autonomous Work

**Date:** 2025-11-24
**Context:** Review of gptme open issues to improve labeling for autonomous agent work
**Duration:** 20 minutes

## Executive Summary

Reviewed 20 open issues in gptme repository to assess autonomous-friendliness and labeling effectiveness. Key finding: **current labels are descriptive but not actionable** - they describe what issues are (enhancement, bug, provider) but don't indicate readiness, difficulty, or suitability for autonomous work.

## Current State

### Label Usage
Current labels observed:
- `enhancement` - Most common, very broad
- `bug` - Problem reports
- `provider` - Provider-related work
- `evals` - Evaluation tasks
- `reliability` - Reliability improvements

### Missing Labels
No labels exist for:
- Difficulty indication (easy/medium/hard)
- Work readiness (ready/blocked/needs-design)
- Work type (autonomous-friendly/needs-human-judgment)
- Help needed signals (good-first-issue/help-wanted)
- Priority indication
- Status (in-progress/has-pr)

## Issues Reviewed

### Complex/Architectural (Not Immediately Autonomous-Friendly)
1. **#790** - GEPA Phase 3.2 integration
   - Complex architectural work
   - No implementation details yet
   - Requires deep context

2. **#774** - Constrained decoding support
   - Well-researched with implementation plan
   - 11-16 hours estimated
   - Could be broken into phases for autonomous work
   - **Recommendation:** Split Phase 1 (OpenAI support, 4-6h) into separate issue

3. **#686** - Lesson system enhancement
   - Multi-phase epic (phases 1-3 done, 4-6 remaining)
   - Very broad scope
   - Phases 4-6 need design decisions

4. **#607** - ToolSpec refactor
   - Architectural changes
   - Needs design

### Well-Defined but Complex
5. **#655** - Ctrl+V paste images
   - Specific, well-described feature
   - UI work with cross-platform considerations
   - Could be autonomous-friendly if broken down

6. **#602** - MCP server interruption bug
   - Needs debugging and signal handling work
   - Requires reproduction and investigation

### Already Being Worked
7. **#492** - Search is broken
   - Has PR #828 working on it (Perplexity search via OpenRouter)
   - **Problem:** No label indicating PR exists or work in progress

## Key Findings

### 1. No Autonomous-Friendly Indicators
- Can't easily identify issues suitable for agent work
- No difficulty signals to filter by complexity
- No readiness indicators to know what's ready to start

### 2. Issue Quality Varies
**Good structure** (#774):
- Background/research section
- Clear implementation plan with phases
- Time estimates
- Success criteria
- Specific code locations

**Needs improvement** (several others):
- Vague descriptions
- No clear next steps
- Missing context
- No acceptance criteria

### 3. Work Status Unclear
- Can't tell which issues are actively being worked
- PR associations not visible in labels
- No blocked/ready distinction

### 4. Priority Not Visible
- All enhancement issues look equal
- No way to identify critical vs. nice-to-have
- No indication of maintainer interest

## Recommendations

### 1. Add Difficulty Labels
- `difficulty: easy` - Simple, well-scoped, <4 hours
- `difficulty: medium` - Moderate complexity, 4-8 hours
- `difficulty: hard` - Complex or architectural, >8 hours

**Benefits:**
- Helps prioritize for available time
- Signals realistic effort expectations
- Enables filtering for autonomous work

### 2. Add Readiness Labels
- `status: ready` - Fully specified, ready to start
- `status: needs-design` - Requires design decisions
- `status: blocked` - Dependencies or blockers exist
- `status: in-progress` - Someone actively working
- `status: has-pr` - Pull request exists

**Benefits:**
- Clear visibility of work status
- Avoid duplicate work
- Focus on actionable items

### 3. Add Work-Type Labels
- `autonomous-friendly` - Suitable for AI agent work
- `needs-human-judgment` - Requires human decision-making
- `good-first-issue` - Good for new contributors
- `help-wanted` - Community contributions welcome

**Benefits:**
- Clear signals for autonomous agents
- Better community engagement
- Appropriate task matching

### 4. Add Priority Labels
- `priority: critical` - Blocks users or development
- `priority: high` - Important for upcoming release
- `priority: medium` - Valuable but not urgent
- `priority: low` - Nice to have

**Benefits:**
- Clear priority signals
- Better resource allocation
- Maintainer intent visible

### 5. Improve Issue Templates

**Suggest template sections:**
```markdown
## Problem/Goal
Clear description of what needs to be solved

## Background
Context, related work, research done

## Implementation Ideas
Potential approaches (if known)

## Acceptance Criteria
How to know when done

## Complexity Estimate
Rough time estimate or complexity indicator

## Dependencies
What needs to happen first
```

### 6. Link PRs to Issues
- Use GitHub's "Closes #X" in PR descriptions
- Add `status: has-pr` label when PR created
- Keep issue open until PR merged

## Specific Issue Recommendations

### Issues That Could Be Made Autonomous-Friendly

1. **#774 Phase 1** - Create new issue for just OpenAI constrained decoding
   - Well-researched and planned
   - 4-6 hours scope
   - Clear implementation path
   - Add: `difficulty: medium`, `status: ready`, `autonomous-friendly`

2. **#655** - Break down Ctrl+V feature
   - Split into: clipboard detection, image handling, UI integration
   - Each phase could be autonomous-friendly
   - Add: `difficulty: hard` (overall), needs breakdown

3. **#686 Phases 4-6** - Create separate issues for each remaining phase
   - Phase 4: Skills integration (needs design)
   - Phase 5: Cursor rules compatibility (could be autonomous-friendly)
   - Phase 6: Documentation (could be autonomous-friendly)

### Issues That Need More Context

1. **#790** - Add implementation approach before labeling
2. **#607** - Add design decisions or RFC first
3. **#492** - Add `status: has-pr` label, link to PR #828

## Action Items for Discussion

1. **Label System:**
   - Propose label taxonomy to maintainers
   - Get feedback on naming conventions
   - Create labels in repository

2. **Issue Improvement:**
   - Identify issues that could be split into smaller, autonomous-friendly tasks
   - Add missing context to vague issues
   - Update issue templates

3. **Process Changes:**
   - Document when to use each label
   - Create workflow for labeling new issues
   - Regular label maintenance (e.g., remove `status: in-progress` when stale)

4. **Pilot Program:**
   - Apply new labels to 5-10 issues as test
   - Get feedback from contributors
   - Iterate on label system

## Benefits of Improved Labeling

### For Autonomous Agents
- Quickly identify suitable work
- Understand complexity before starting
- Avoid blocked or in-progress items
- Focus on high-priority needs

### For Human Contributors
- Better issue discovery
- Clear expectations
- Reduced duplicate work
- Community-friendly issues visible

### For Maintainers
- Communicate priority clearly
- Better work distribution
- Track issue status easily
- Measure completion rates

## Next Steps

1. **Create RFC/Discussion Issue**
   - Present this analysis
   - Propose label taxonomy
   - Get maintainer feedback

2. **Pilot Test**
   - Apply labels to subset of issues
   - Test with autonomous work sessions
   - Gather feedback

3. **Rollout**
   - Create all labels
   - Document usage guidelines
   - Apply to existing issues
   - Update issue templates

4. **Maintain**
   - Regular label reviews
   - Remove stale status labels
   - Update as needs evolve

## Conclusion

The current issue labeling system provides basic categorization but lacks the granularity needed for effective autonomous work identification. By implementing a structured label taxonomy with difficulty, readiness, work-type, and priority indicators, we can:

1. Enable autonomous agents to identify suitable work quickly
2. Improve contributor experience with clear expectations
3. Increase project velocity through better work distribution
4. Maintain better visibility of project status

The key is moving from **descriptive labels** (what is it?) to **actionable labels** (can I work on it? how hard? how important?).

This requires:
- Maintainer buy-in for label taxonomy
- Process for applying labels consistently
- Regular maintenance to keep labels current
- Community education on label meanings

**Recommendation:** Start with a small pilot on 5-10 issues to validate the approach, then expand based on feedback.

---

**Related:**
- Manual work queue: `/home/bob/alice/state/queue-manual.md` item #3
- Task system: `/home/bob/alice/TASKS.md`
- Issue listing: https://github.com/gptme/gptme/issues
