# CI Failure Investigation

## Overview

This document describes the systematic approach to investigating Continuous Integration (CI) test failures, distinguishing between different failure types, and determining appropriate actions. This pattern emerged from investigating test failures across multiple gptme PRs.

## Failure Types

### 1. PR-Specific Failures

**Characteristics**:
- Tests pass on master/main branch
- Tests fail only on the PR branch
- Introduced by PR changes

**Response**: Fix the PR code or tests

### 2. Pre-Existing Failures

**Characteristics**:
- Tests also fail on master/main branch
- Failure exists before PR was created
- PR changes are not the cause

**Response**: Document that failures are pre-existing, proceed with PR review

### 3. Transient Failures

**Characteristics**:
- Intermittent failures
- Network/infrastructure issues
- Race conditions

**Response**: Retry CI or investigate flaky tests

### 4. Dependency Failures

**Characteristics**:
- External dependency issues
- API changes
- Version conflicts

**Response**: Update dependencies or pin versions

## Investigation Workflow

### Step 1: Identify the Failure

**Check PR CI status**:
```bash
# Quick status check
gh pr view <pr-number> --json statusCheckRollup --jq '.statusCheckRollup[] | select(.conclusion != "SUCCESS") | "\(.name): \(.conclusion)"'

# Get full CI run list
gh run list --pr <pr-number> --limit 5
```

**Look for**:
- Which workflows failed?
- Which jobs/steps failed?
- Is it consistent across retries?

### Step 2: Get Failure Details

**View failed logs**:
```bash
# Show failed logs only (most efficient)
gh run view <run-id> --log-failed

# Full logs if needed
gh run view <run-id> --log
```

**Extract key information**:
- Error message
- Stack trace
- Failed test name
- Failure context

### Step 3: Check Master Branch

**Critical step: Determine if failure is pre-existing**:
```bash
# Get recent master runs
gh run list --branch master --workflow "Test" --limit 5 --json databaseId,conclusion,displayTitle,createdAt

# View specific master run
gh run view <master-run-id> --log-failed

# Search for same error in master logs
gh run view <master-run-id> --log-failed | grep -A 10 "error_pattern"
```

**If same test fails on master**:
- Failure is **pre-existing**
- Not caused by the PR
- Document this finding

**If test passes on master**:
- Failure is **PR-specific**
- Caused by PR changes
- Needs investigation and fix

### Step 4: Cross-PR Analysis

**When multiple PRs fail similarly**:

```bash
# List recent PRs
gh pr list --limit 10

# Check CI status across multiple PRs
for pr in 862 863 865; do
  echo "PR #$pr:"
  gh pr view $pr --json statusCheckRollup --jq '.statusCheckRollup[] | select(.conclusion == "FAILURE") | .name'
done
```

**Identify patterns**:
- Same test failing across PRs?
- Same error message?
- Recent change on master that broke tests?

### Step 5: Document Findings

**For pre-existing failures**:
```markdown
## Test Failure Analysis

**Status**: Test failures are pre-existing on master, not caused by this PR.

### Failed Test
`tests/test_module.py::test_name`
- Error: `AssertionError: expected X, got Y`
- First appeared: <date> in commit <sha>
- Affects PRs: #XXX, #YYY, #ZZZ

### Evidence
- Master CI run: <url>
- Master commit with failure: <sha>
- PR CI run: <url>

### Recommendation
PR code changes are sound. Test needs to be fixed separately on master.

### Upstream Tracking
- Issue: #<issue-number> (if exists)
- Status: Investigating/Waiting for fix
```

**For PR-specific failures**:
```markdown
## CI Failure Report

**Status**: Test failure introduced by this PR.

### Failed Test
`tests/test_module.py::test_name`
- Error: `<specific error>`
- Caused by: <specific change>

### Root Cause
<explanation of what change caused the failure>

### Suggested Fix
<specific recommendation with code if possible>
```

## Common Failure Patterns

### Pattern: DSPy Test Structure Failure

**Symptom**:
AssertionError: assert 'focus_areas' in {}

**Context**: DSPy test expecting specific task structure

**Investigation**:
```bash
# Check if fails on master
gh run list --branch master --workflow "Test" --limit 5

# View master failure
gh run view <master-run-id> --log-failed | grep -A 20 "test_dspy_basic"
```

**Resolution**: Pre-existing failure on master, document and track

### Pattern: Import Errors

**Symptom**:
ModuleNotFoundError: No module named 'package_name'

**Context**: Missing dependency or incorrect import path

**Investigation**:
```bash
# Check pyproject.toml for dependency
grep -A 5 "dependencies" pyproject.toml

# Check if package is installed in CI environment
gh run view <run-id> --log | grep "pip list"
```

**Resolution**: Add missing dependency or fix import path

### Pattern: Transient Network Failures

**Symptom**:
ConnectionError: Failed to connect to external service
TimeoutError: Request timed out after 30s

**Context**: Network or external API issues

**Investigation**:
```bash
# Check if other CI runs had same issue
gh run list --limit 10 | grep -i "failure\|cancelled"

# Look for retry success
gh run list --pr <pr-number> --limit 3
```

**Resolution**: Retry CI run, or add proper timeout/retry logic

### Pattern: Flaky Tests

**Symptom**: Intermittent failures on same test

**Investigation**:
```bash
# Check test history across multiple runs
for run in $(gh run list --limit 10 --json databaseId --jq '.[].databaseId'); do
  echo "Run $run:"
  gh run view $run --log-failed | grep "test_flaky_name" || echo "Passed"
done
```

**Resolution**: Fix race conditions, add proper waits, or mark as flaky

## Best Practices

### 1. Always Check Master First

**Why**: Distinguishing PR-specific from pre-existing failures is critical

**How**:
```bash
# Standard workflow
gh run list --branch master --workflow "Test" --limit 3
gh run view <most-recent-master-run> --log-failed
```

**Benefits**:
- Avoid wasting time on upstream issues
- Provide accurate PR feedback
- Track systemic problems

### 2. Use Grep for Large Logs

**Why**: CI logs can be massive (thousands of lines)

**How**:
```bash
# Find specific error
gh run view <run-id> --log-failed | grep -A 10 "AssertionError"

# Find test failure
gh run view <run-id> --log-failed | grep -B 5 -A 15 "FAILED tests/"

# Count occurrences
gh run view <run-id> --log | grep -c "test_name"
```

**Benefits**:
- Faster investigation
- Lower token usage
- Focus on relevant errors

### 3. Document Cross-PR Patterns

**Why**: Same issue often affects multiple PRs

**How**:
- Create comprehensive analysis on one PR
- Reference from other affected PRs
- Track in work queue for monitoring

**Benefits**:
- Avoid duplicate investigation
- Provide maintainers with context
- Track resolution progress

### 4. Use Token-Efficient Commands

**Good** (filtered output):
```bash
gh run view <run-id> --log-failed
gh run list --limit 5 --json databaseId,conclusion
```

**Bad** (massive output):
```bash
gh run view <run-id> --log  # Full logs
gh run list                 # All runs
```

### 5. Verify Before Commenting

**Checklist before posting CI analysis**:
- [ ] Checked master branch status
- [ ] Reviewed actual error logs
- [ ] Identified root cause
- [ ] Verified across multiple runs if needed
- [ ] Provided evidence (links, commits)
- [ ] Gave clear recommendation

## Investigation Checklist

### For Any CI Failure

1. **Identify**:
   - [ ] Which workflow failed?
   - [ ] Which job/step failed?
   - [ ] What's the error message?

2. **Compare**:
   - [ ] Does same test pass on master?
   - [ ] Do other recent PRs fail similarly?
   - [ ] Is this a known issue?

3. **Analyze**:
   - [ ] What's the root cause?
   - [ ] Is it PR-specific or pre-existing?
   - [ ] Is it transient or reproducible?

4. **Document**:
   - [ ] Post findings to PR
   - [ ] Link to evidence
   - [ ] Provide recommendations
   - [ ] Update journal entry

5. **Track**:
   - [ ] Add to work queue if monitoring needed
   - [ ] Create issue if systemic problem
   - [ ] Follow up on resolution

## Tools Reference

### gh run list

**Purpose**: List CI workflow runs

**Common Usage**:
```bash
# Recent runs on branch
gh run list --branch <branch> --workflow "Test" --limit 5

# Runs for specific PR
gh run list --pr <pr-number> --limit 3

# Filter by status
gh run list --status failure --limit 10

# Custom fields
gh run list --json databaseId,conclusion,displayTitle,createdAt
```

### gh run view

**Purpose**: View CI run details and logs

**Common Usage**:
```bash
# Failed logs only (recommended)
gh run view <run-id> --log-failed

# Full logs (large output)
gh run view <run-id> --log

# Summary only
gh run view <run-id>
```

### gh pr checks

**Purpose**: Quick PR CI status

**Usage** (via gh tool, not shell):
```gh pr checks <pr-url>
<optional-commit-sha>
```

**Benefits**:
- Wait for all checks to complete
- Quick status overview
- Specific commit checking

### gh pr status

**Purpose**: Quick CI health check

**Usage** (via gh tool, not shell):
```gh pr status <pr-url>
<optional-commit-sha>
```

**Benefits**:
- Summary of check states
- Failed run IDs
- Total pass/fail counts

## Time Management

### Quick Checks (1-2 minutes)
- PR CI status overview
- Master branch comparison
- Recent run history

### Standard Investigation (5-10 minutes)
- Read full error logs
- Compare with master
- Document findings
- Post PR comment

### Deep Investigation (15-30 minutes)
- Reproduce locally
- Test fixes
- Cross-PR analysis
- Comprehensive documentation

## Related

- [GitHub PR Review Workflow](./github-pr-review-workflow.md) - Overall PR review process
- [Error Recovery](./error-recovery.md) - Handling investigation failures
- [Verification Strategies](./verification-strategies.md) - Testing approaches
- [Token Efficiency Patterns](./token-efficiency-patterns.md) - Optimizing log reading

## Examples

### Example 1: Quick Pre-Existing Check

```bash
# PR #863 failing test
gh run view <pr-run-id> --log-failed | grep "test_dspy_basic"

# Check master
gh run list --branch master --workflow "Test" --limit 3
gh run view <master-run-id> --log-failed | grep "test_dspy_basic"

# Result: Same failure on master → Pre-existing
```

### Example 2: Cross-PR Pattern Investigation

```bash
# Check multiple PRs for same failure
for pr in 862 863 865; do
  echo "=== PR #$pr ==="
  gh pr view $pr --json statusCheckRollup --jq '.statusCheckRollup[] | select(.name | contains("Test")) | "\(.name): \(.conclusion)"'
done

# Result: All three PRs fail same test → Systemic issue
```

### Example 3: Monitoring CI Resolution

```bash
# Check if master fix has landed
gh run list --branch master --workflow "Test" --limit 5 --json databaseId,conclusion,createdAt

# Check specific run
gh run view <recent-master-run> --log-failed | grep "test_dspy_basic" || echo "Test now passes!"

# Update affected PRs when fixed
```

## Maintenance

Update this document when:
- New CI failure patterns emerge
- GitHub CLI adds new features
- Investigation techniques improve
- Time estimates need adjustment

Last updated: 2025-11-21
