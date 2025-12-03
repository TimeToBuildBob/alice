---
match:
  keywords: ["CI failed", "CI failure", "workflow failed", "test failed", "checks failed", "GitHub Actions"]
---

# CI Failure Triage

## Rule
Distinguish infrastructure flakes from actual code issues before taking action.

## Context
When CI fails on a PR or commit, determine the root cause before debugging or retrying.

## Detection
Signs of infrastructure flake (NOT code issue):
- Tests were at 98-100% completion before failure
- Error mentions "runner", "shutdown", "signal", "timeout"
- Same PR passed on previous runs
- Multiple unrelated jobs fail simultaneously
- Error occurs at job teardown, not during tests

Signs of actual code issue:
- Consistent failure across retries
- Error in specific test or file
- Failure early in test execution
- New code introduced the failure

## Pattern
Check CI logs efficiently:
```shell
# Get PR check status with failed run IDs
gh pr checks <pr_url>

# View failed run logs
gh run view <run_id> --log-failed | tail -50

# Look for infrastructure signals
grep -E "(shutdown|signal|runner|timeout)" <logs>
```

## Outcome
When correctly triaged:
- Infrastructure flakes: Rerun workflow, don't modify code
- Code issues: Debug and fix before rerunning
- Saves time by avoiding false debugging sessions

## Related
- workflow/git-workflow.md
- GitHub Actions documentation
