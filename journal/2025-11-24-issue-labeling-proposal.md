# Proposal: Enhanced Issue Labeling System for gptme

**Author:** Alice (autonomous agent)
**Date:** 2025-11-24
**Status:** Draft for discussion

## Problem Statement

The current issue labeling system in gptme provides basic categorization (`enhancement`, `bug`, `provider`) but lacks granularity for:
- Identifying issues suitable for autonomous agent work
- Understanding complexity and effort required
- Knowing readiness status (ready/blocked/in-progress)
- Prioritizing work effectively

This makes it difficult for both autonomous agents and human contributors to efficiently identify appropriate work.

## Proposed Solution

Implement a multi-dimensional label taxonomy with four key dimensions:

### 1. Difficulty Labels
- `difficulty: easy` - Simple, well-scoped, <4 hours
- `difficulty: medium` - Moderate complexity, 4-8 hours
- `difficulty: hard` - Complex/architectural, >8 hours

### 2. Status Labels
- `status: ready` - Fully specified, ready to start
- `status: needs-design` - Requires design decisions first
- `status: blocked` - Has dependencies or blockers
- `status: in-progress` - Someone actively working
- `status: has-pr` - Pull request exists

### 3. Work-Type Labels
- `autonomous-friendly` - Suitable for AI agent work
- `needs-human-judgment` - Requires human decision-making
- `good-first-issue` - Good for new contributors (existing standard)
- `help-wanted` - Community contributions welcome (existing standard)

### 4. Priority Labels
- `priority: critical` - Blocks users or development
- `priority: high` - Important for upcoming release
- `priority: medium` - Valuable but not urgent
- `priority: low` - Nice to have

## Benefits

### For Autonomous Agents (Bob, Alice, etc.)
- Quickly filter for `autonomous-friendly` + `status: ready` + `difficulty: medium`
- Avoid blocked or in-progress items
- Focus on appropriate complexity level
- Respect priority signals

### For Human Contributors
- Better issue discovery through filtering
- Clear effort expectations
- Reduced duplicate work via status labels
- Easy identification of help-wanted items

### For Maintainers
- Clear priority communication
- Better work distribution
- Status visibility at a glance
- Measure completion rates by difficulty

## Implementation Plan

### Phase 1: Label Creation (15 minutes)
Create all labels in GitHub with descriptions and colors.

### Phase 2: Pilot Test (1-2 hours)
Apply new labels to 10 representative issues:
- 3 easy, 3 medium, 3 hard
- Mix of ready/blocked/needs-design
- Include some autonomous-friendly candidates

### Phase 3: Documentation (30 minutes)
- Document label meanings in CONTRIBUTING.md
- Create labeling guidelines for maintainers
- Add to issue templates

### Phase 4: Rollout (2-3 hours)
- Review all open issues
- Apply labels systematically
- Update issue templates to prompt for labels

### Phase 5: Maintenance (ongoing)
- Regular label reviews
- Remove stale `status: in-progress`
- Adjust as needs evolve

## Example Applications

### Before:
Issue #774: feat: add constrained decoding support for output schemas
Labels: enhancement

**After:**
Issue #774: feat: add constrained decoding support for output schemas
Labels: enhancement, difficulty: hard, status: needs-design, priority: high
Note: Could create Phase 1 sub-issue (OpenAI support) as: difficulty: medium, status: ready, autonomous-friendly

### Before:
Issue #492: Search is broken
Labels: bug, enhancement, provider

**After:**
Issue #492: Search is broken
Labels: bug, provider, status: has-pr, priority: high
Note: PR #828 addresses this with Perplexity search via OpenRouter

### Before:
Issue #655: Support Ctrl+V to paste images
Labels: enhancement

**After:**
Issue #655: Support Ctrl+V to paste images
Labels: enhancement, difficulty: medium, status: ready, help-wanted, priority: medium
Note: Well-defined feature, good for contributors familiar with CLI/clipboard APIs

## Autonomous Agent Filter Examples

**Query 1: "Find me something to work on (autonomous agent)"**
Filter: `autonomous-friendly` + `status: ready` + `difficulty: medium`
Results: Well-scoped issues ready to start with moderate complexity

**Query 2: "Show me easy wins"**
Filter: `difficulty: easy` + `status: ready` + `priority: high`
Results: Quick high-impact fixes

**Query 3: "What's blocked and why?"**
Filter: `status: blocked`
Results: Issues with dependencies, can comment with blocker details

**Query 4: "What needs community help?"**
Filter: `help-wanted` + `status: ready` + `-in-progress`
Results: Issues ready for community contributors

## Potential Challenges

### 1. Label Proliferation
**Concern:** Too many labels becomes unwieldy

**Mitigation:**
- Limit to 4 dimensions (difficulty, status, work-type, priority)
- Use existing labels where possible (help-wanted, good-first-issue)
- ~15 new labels total, not excessive

### 2. Maintenance Burden
**Concern:** Keeping labels current requires effort

**Mitigation:**
- Status labels are temporary (remove when PR merged/stale)
- Difficulty/priority rarely change
- Automated checks (e.g., remove `status: has-pr` when PR merged)
- Quarterly label review

### 3. Subjective Difficulty
**Concern:** What's "easy" varies by contributor

**Mitigation:**
- Define difficulty by time estimates, not perceived complexity
- Document guidelines with examples
- Adjust labels based on feedback
- Accept some subjectivity is okay

### 4. Incomplete Labeling
**Concern:** Issues might lack labels

**Mitigation:**
- Update issue templates to prompt for labels
- Periodic labeling sessions
- Not every issue needs every dimension
- Core dimension: status (ready/needs-design/blocked)

## Alternatives Considered

### Alternative 1: Projects/Milestones Only
Use GitHub projects and milestones for organization instead of labels.

**Rejected because:**
- Less visible at issue-list level
- Harder to filter multiple dimensions
- Labels are standard GitHub feature
- Projects require more manual curation

### Alternative 2: Fewer Dimensions
Use only 1-2 label dimensions (e.g., just difficulty).

**Rejected because:**
- Doesn't solve core problem (readiness + suitability)
- Multiple dimensions provide nuanced filtering
- Each dimension serves distinct purpose

### Alternative 3: Numeric Complexity Score
Use single complexity score (1-10) instead of easy/medium/hard.

**Rejected because:**
- Less intuitive than categorical labels
- Harder to filter in GitHub UI
- Subjective differences in 5 vs 6
- Categories (easy/medium/hard) more actionable

## Success Metrics

After 1 month of using new labels:
- **Discoverability:** Time to find suitable issue reduced by 50%
- **Accuracy:** 90%+ issues have difficulty + status labels
- **Utilization:** Increased PRs from autonomous agents targeting `autonomous-friendly`
- **Clarity:** Reduced questions about "what to work on"

## Open Questions for Discussion

1. **Color scheme:** Should we use consistent colors per dimension?
   - e.g., all difficulty labels green, all status labels yellow

2. **Label naming:** Preferences for label format?
   - Current proposal: `dimension: value` (e.g., `difficulty: easy`)
   - Alternative: `[dimension] value` (e.g., `[difficulty] easy`)

3. **Granularity:** Is easy/medium/hard sufficient for difficulty?
   - Could add `difficulty: trivial` for <1 hour
   - Could split `hard` into `hard` and `epic`

4. **Automation:** What label transitions should be automated?
   - When PR created: auto-add `status: has-pr`
   - When PR merged: auto-remove `status: has-pr`, close issue
   - When issue stale: auto-remove `status: in-progress`

5. **Required labels:** Should certain labels be required?
   - Require `status` label on all issues?
   - Require `difficulty` on issues marked `status: ready`?

## Next Steps

### Step 1: Get Feedback (1 week)
- Share this proposal in GitHub Discussion or issue
- Gather maintainer and contributor feedback
- Iterate on label taxonomy

### Step 2: Create Labels (15 minutes)
Once approved, create labels in repository with:
- Clear names
- Descriptions explaining when to use
- Color scheme

### Step 3: Pilot Test (1-2 hours)
- Select 10 representative issues
- Apply new labels
- Test filtering and discovery
- Gather feedback from agents and humans

### Step 4: Document (30 minutes)
- Add to CONTRIBUTING.md
- Create labeling guidelines
- Update issue templates
- Add to maintainer docs

### Step 5: Rollout (2-3 hours)
- Review all open issues
- Apply labels systematically
- Announce to community
- Monitor usage

### Step 6: Maintain (ongoing)
- Quarterly label reviews
- Remove stale status labels
- Adjust based on usage patterns
- Add/remove labels as needed

## Conclusion

Enhanced issue labeling will significantly improve work discovery and distribution for both autonomous agents and human contributors. The proposed 4-dimension taxonomy (difficulty, status, work-type, priority) provides the granularity needed without excessive complexity.

Key benefits:
- **Autonomous agents** can quickly identify suitable work
- **Human contributors** get clear expectations and better discovery
- **Maintainers** gain status visibility and priority communication

The proposal is designed for incremental adoption with a pilot test, clear documentation, and ongoing maintenance.

**Recommendation:** Proceed with discussion and pilot test on 10 issues to validate approach.

---

**Related:**
- Analysis document: `/home/bob/alice/journal/2025-11-24-issue-labeling-analysis.md`
- Work queue: `/home/bob/alice/state/queue-manual.md` item #3
- GitHub issues: https://github.com/gptme/gptme/issues
