# DSPy Test Fix: Metadata Registration

**Date**: 2025-11-21
**Session**: Autonomous Run (14:49 UTC)
**Issue**: #866
**PR**: #867

## Summary

Fixed the root cause of DSPy test failures (`test_task_structure`) that were blocking multiple PRs. The issue was that two task creation functions weren't registering their metadata, causing tests to fail when checking for `focus_areas`.

## Problem Analysis

The test `test_task_structure` was failing with:
```python
AssertionError: assert 'focus_areas' in {}
```

Investigation revealed:
- `create_medium_complexity_task()` and `create_complex_task()` were returning raw `EvalSpec` dicts
- They weren't using the `TaskBuilder.build_with_metadata()` pattern
- Metadata wasn't being registered in the `_task_metadata` global registry
- When tests called `get_task_metadata(task_name)`, it returned `{}` for these tasks

## Solution

Converted both functions to follow the established pattern:

1. **Use TaskBuilder pattern**: Instead of raw dict literals
2. **Call build_with_metadata()**: Returns tuple of (spec, metadata)
3. **Register metadata**: Store in `_task_metadata[spec["name"]] = metadata`
4. **Add focus_areas**:
   - Medium task: `["system_design", "testing", "complexity_medium"]`
   - Complex task: `["performance_optimization", "profiling", "complexity_high"]`

## Code Changes

**File**: `gptme/eval/dspy/tasks.py`

Both functions now follow this pattern:
```python
def create_medium_complexity_task() -> EvalSpec:
    spec, metadata = (
        TaskBuilder()
        .with_name("implement-caching-system")
        .with_run("python test_cache.py")
        .with_prompt("...")
        .with_tools(["save", "shell", "read"])
        .with_focus(["system_design", "testing", "complexity_medium"])
        .build_with_metadata()
    )
    _task_metadata[spec["name"]] = metadata
    return spec
```

## Impact

- **Unblocks PRs**: #861, #863, and potentially others
- **Consistency**: All task creation now uses the same pattern
- **Functionality**: Focus area filtering system works correctly for all tasks

## Testing

The fix should allow `test_task_structure` to pass. Once CI runs complete on PR #867, we'll verify:
- Test passes successfully
- All task metadata is properly registered
- Focus area filtering works as expected

## Next Steps

- Wait for CI checks on PR #867
- If tests pass, this will unblock the other waiting PRs
- Consider adding validation to ensure all tasks register metadata (prevent future issues)

## Related

- Issue #866: Original issue documenting the test failures
- PR #867: This fix
- PRs blocked: #861 (patch whitespace), #863 (server defaults)
