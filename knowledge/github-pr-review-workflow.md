# GitHub PR Review Workflow

## Overview

This document describes the systematic workflow for reviewing and analyzing GitHub Pull Requests, particularly for the gptme project. This pattern emerged from multiple PR reviews conducted during autonomous operations.

## Core Workflow

### 1. Initial PR Assessment

**Gather Basic Information**:
```bash
# View PR metadata
gh pr view <pr-number> --json number,title,state,author,updatedAt,additions,deletions

# Check current CI status
gh pr view <pr-number> --json statusCheckRollup --jq '.statusCheckRollup[] | select(.conclusion != "SUCCESS") | "\(.name): \(.conclusion)"'
```

**Key Questions**:
- What is the PR trying to accomplish?
- Is CI passing or failing?
- When was it last updated?
- What's the size/complexity (additions/deletions)?

### 2. Read Full PR Context

**Use the gh pr view tool** (not shell commands) to get comprehensive context:
```gh pr view <pr-url>
None
```

This provides:
- PR description and motivation
- Review comments with context
- Code suggestions
- Discussion threads
- Full conversation history

**Why not shell?**
- `gh pr view <number>` via shell omits crucial context (review comments, code context)
- The dedicated `gh pr view` tool is optimized for PR analysis
- Shell commands require multiple calls to piece together context

### 3. Analyze Test Failures

**For failing PRs, determine failure type**:

```bash
# Check recent CI runs
gh run list --repo <owner/repo> --pr <number> --limit 3

# View specific run logs (if needed)
gh run view <run-id> --log-failed
```

**Failure Classification**:
1. **PR-Specific**: Introduced by the PR changes
2. **Pre-Existing**: Already failing on master/main branch
3. **Transient**: Infrastructure/network issues

**For Pre-Existing Failures**:
```bash
# Check master branch status
gh run list --repo <owner/repo> --branch master --workflow "Test" --limit 5

# Verify same test fails on master
gh run view <master-run-id> --log-failed | grep -A 10 "test_name"
```

### 4. Cross-PR Pattern Recognition

**When multiple PRs fail similarly**:

1. **Document the pattern** - Create comprehensive analysis:
   - Which PRs are affected?
   - What's the common failure?
   - Is it on master or PR-specific?
   - What's the root cause?

2. **Post analysis to one representative PR** - Choose the most recent or most discussed

3. **Reference from other PRs** - Brief comment pointing to detailed analysis

**Example Structure**:
```markdown
## Test Failure Analysis

**Status**: Test failures are pre-existing on master, not caused by this PR.

### Failed Test
`test_path::test_name`
- Error: `specific error message`
- Root cause: `explanation`

### Evidence
- Reproduced on master: <commit-sha>
- Affected PRs: #XXX, #YYY, #ZZZ
- CI run: <master-run-url>

### Recommendation
PR changes are sound. Test failures need to be addressed separately on master.
```

### 5. Verify Code Changes

**For complex changes**:
1. Check out the PR branch locally using worktrees
2. Review the actual diff
3. Run local tests if needed
4. Verify review comments are addressed

```bash
# Create worktree for PR
git worktree add ../gptme-<issue-number> pr-branch

# Review changes
cd ../gptme-<issue-number>
git diff master...HEAD
```

### 6. Comment and Document

**Effective PR Comments**:
- Lead with conclusion (✅/❌/⚠️)
- Provide evidence (links to CI runs, master comparisons)
- Be specific about next steps
- Use clear formatting (headers, lists, code blocks)

**Journal Entry**:
- Document the review process
- Note key findings
- Record time spent
- Link to PRs and comments

## Common Patterns

### Pattern: Pre-Existing Test Failures

**Symptom**: PR fails tests that also fail on master

**Investigation**:
```bash
# 1. Identify failing test
gh pr view <pr> --json statusCheckRollup

# 2. Check if same test fails on master
gh run list --branch master --workflow "Test" --limit 3
gh run view <master-run-id> --log-failed

# 3. Document findings
# Post comment: "Test failures are pre-existing, not introduced by PR"
```

### Pattern: Review Comment Verification

**Symptom**: PR has unresolved review comments

**Investigation**:
```bash
# 1. Read full PR context including reviews
gh pr view <pr-url>

# 2. Check if concerns are addressed in latest code
cd <worktree>
git log --oneline --since="<last-review-date>"
git show <commit>

# 3. Verify specific concerns
# Look for the code changes that address each comment
```

### Pattern: CI Status Check

**Symptom**: Need to quickly assess PR health

**Quick Check**:
```bash
# One-liner for CI status
gh pr view <pr> --json statusCheckRollup --jq '.statusCheckRollup[] | select(.conclusion != "SUCCESS") | "\(.name): \(.conclusion)"'

# Check if ready for review
gh pr view <pr> --json state,isDraft,mergeable
```

## Best Practices

### 1. Read Full Context First

**Always use the gh pr view tool for initial reading**:
- Don't piece together context from multiple shell commands
- Get review comments with code context
- Understand the full conversation

### 2. Verify Claims

**Don't trust PR descriptions alone**:
- Check CI logs for actual errors
- Reproduce failures if needed
- Compare with master branch state

### 3. Cross-Reference PRs

**When issues affect multiple PRs**:
- Document once comprehensively
- Link between related PRs
- Update as situation evolves

### 4. Provide Actionable Feedback

**Good Comments**:
- ✅ "Tests are pre-existing failures on master (link). PR changes are good."
- ✅ "CI failure in test_x due to missing import. Suggest adding..."
- ✅ "All review comments addressed in commit abc123. Ready for merge."

**Bad Comments**:
- ❌ "Tests failing" (not actionable)
- ❌ "Looks good" (not specific)
- ❌ "Maybe try..." (uncertain)

### 5. Document Efficiently

**In journal entries**:
- Focus on findings and decisions
- Link to PRs and comments
- Keep it concise (5-10 lines for PR reviews)
- Don't repeat entire work queue

### 6. Use Worktrees for Deep Dives

**When to use worktrees**:
- Complex changes requiring local testing
- Need to verify multiple commits
- Want to experiment with fixes
- Reviewing code structure changes

**When shell commands suffice**:
- Quick CI status checks
- Reading PR descriptions
- Checking recent comments

## Tools Reference

### gh pr view (Tool)

**Purpose**: Read full PR context including review comments

**Usage**:
```gh pr view <pr-url>
None
```

**Provides**:
- PR description and metadata
- Review comments with code context
- Code suggestions from reviewers
- Full conversation thread

### gh pr view (Shell)

**Purpose**: Quick metadata queries

**Usage**:
```bash
gh pr view <number> --json <fields>
```

**Use for**:
- CI status checks
- Basic metadata (state, author, dates)
- Mergability status
- Quick filters in scripts

### gh run commands

**Purpose**: CI/CD workflow investigation

**Common Commands**:
```bash
# List recent runs
gh run list --branch <branch> --workflow <workflow> --limit N

# View specific run
gh run view <run-id>

# View failed logs
gh run view <run-id> --log-failed
```

## Related

- [Git Worktree Workflow](./git-worktree-workflow.md) - For managing multiple PR branches
- [Error Recovery](./error-recovery.md) - Handling PR review failures
- [Task State Management](./task-state-management.md) - Tracking PR review tasks
- [Verification Strategies](./verification-strategies.md) - Testing PR changes

## Examples

### Example 1: Simple CI Failure Check

```bash
# Check PR CI status
gh pr view 863 --json statusCheckRollup --jq '.statusCheckRollup[] | select(.conclusion != "SUCCESS")'

# Result: Test failures found
# Next: Check if pre-existing on master
gh run list --branch master --workflow "Test" --limit 3
```

### Example 2: Review Comment Verification

```gh pr view https://github.com/ErikBjare/gptme/pull/863
None
```

Read all review comments with code context, then verify each is addressed in the code.

### Example 3: Cross-PR Analysis

**Scenario**: Multiple PRs (#862, #863, #865) failing same test

**Steps**:
1. Document comprehensive analysis on one PR (#863)
2. Post brief comments on others referencing the analysis
3. Monitor master branch for fix
4. Update all PRs when resolved

## Maintenance

This workflow should be updated when:
- New PR review patterns emerge
- GitHub CLI adds new features
- gptme tooling changes
- Better practices are discovered

Last updated: 2025-11-21
