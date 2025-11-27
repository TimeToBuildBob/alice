# Issue #408: Diagnostic Logging Implementation

**Date**: 2025-11-27
**Issue**: https://github.com/gptme/gptme/issues/408
**PR**: https://github.com/gptme/gptme/pull/890
**Status**: PR created, CI checks running

## Summary

Implemented Solution 2 (Diagnostic Logging - LOW RISK, RECOMMENDED) from the investigation to help diagnose shell tool output confusion.

## Implementation

Added debug-level logging to `gptme/tools/shell.py` in the `_run()` method at three key points:

1. **Command start** (line ~285):
   - Logs: `"Shell: Running command: {command[:200]}"`
   - Purpose: Track when command execution begins
   - Truncation: 200 chars to avoid log spam

2. **Delimiter detection** (line ~392):
   - Logs: `"Shell: Delimiter detected in line: {line.strip()[:200]}"`
   - Purpose: Track when completion delimiter is found
   - Truncation: 200 chars

3. **Pre-delimiter stdout capture** (line ~398):
   - Logs: `"Shell: Last stdout before delimiter: {last_stdout}"`
   - Purpose: Capture last 3 lines of stdout to detect unexpected content
   - Truncation: 300 chars

## Technical Details

- **Log Level**: DEBUG (opt-in via configuration)
- **Impact**: None on normal operation, purely diagnostic
- **Risk**: LOW - additive only, no functional changes
- **Syntax**: Validated with `py_compile`

## Branch Management

Created clean branch from master:
1. Initial commit on wrong branch (pr-888)
2. Used `git reset --soft HEAD~1` to uncommit from pr-888
3. Created `issue-408-diagnostic-logging` branch from master
4. Cherry-picked commit `ebe314ec4`
5. Pushed to origin

## PR Details

**PR #890**: feat: add diagnostic logging to shell tool for Issue #408
- Created with comprehensive description
- Posted comment on issue #408 linking to PR
- CI checks in progress (10 checks running)

## Next Steps

1. **Wait for CI**: Monitor CI checks completion
2. **Address feedback**: Respond to any review comments
3. **Merge**: Once approved and CI passes
4. **Deploy**: After merge, enable DEBUG logging in affected environments
5. **Data collection**: Gather diagnostic logs when issue occurs
6. **Analysis**: Analyze patterns to identify root cause
7. **Next solution**: Implement additional fixes based on findings

## Benefits

- Provides production visibility into shell execution flow
- Helps identify when/how output confusion occurs
- Enables data-driven approach to root cause analysis
- Low risk, high value diagnostic capability
- Foundation for implementing more targeted fixes

## References

- Investigation: journal/2025-11-27-issue408-shell-reliability-investigation.md
- Issue: https://github.com/gptme/gptme/issues/408
- PR: https://github.com/gptme/gptme/pull/890
- Commit: ebe314ec4
