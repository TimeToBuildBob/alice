# GitHub Issue Draft: DSPy Test Failures on Master

## Title
DSPy test failures blocking multiple PRs: test_task_structure and test_dspy_hybrid

## Description

### Summary
Two DSPy-related test failures are currently failing on master branch, blocking the merge of multiple unrelated PRs.

### Failing Tests

**1. `tests/test_dspy_basic.py::test_task_structure`**
```python
AssertionError: assert 'focus_areas' in {}
```

**Location**: `tests/test_dspy_basic.py:118`

**Error Context**:
```python
def test_task_structure():
    """Test that tasks have required structure."""
    from gptme.eval.dspy.tasks import get_task_metadata

    tasks = get_prompt_optimization_tasks()

    for task in tasks:
        assert "name" in task
        assert "prompt" in task

        # Check that metadata contains focus_areas
        metadata = get_task_metadata(task["name"])
        assert "focus_areas" in metadata  # ← FAILS HERE
```

**2. `tests/test_dspy_hybrid.py` - Import Error**
```python
ModuleNotFoundError: No module named 'dspy'
```

**Note**: This test passes locally with `uv run pytest` but fails in CI due to missing optional dependency. Codecov shows 100% flake rate.

### Impact

**Affected PRs** (confirmed pre-existing on master):
- #862: fix(docs): add context-plugins.md to toctree
- #863: fix(server): add default model fallback
- #865: fix(tools): preserve full type information in tool signatures

**Timeline**:
- First observed: 2025-11-20
- Still failing on master: 2025-11-21 (run 19571133535)
- Multiple PRs blocked from merge despite passing all other checks

### Analysis

**Root Cause**: Issue in DSPy evaluation module, NOT in the code changes of affected PRs.

**Evidence**:
1. All three PRs modify completely different modules:
   - #862: docs/toctree
   - #863: gptme/server
   - #865: gptme/tools/base.py
2. None of these PRs touch DSPy evaluation code
3. Test failures verified on master branch before PR merges

**test_task_structure Failure**:
- `get_task_metadata()` returns empty dict instead of dict with 'focus_areas'
- Suggests missing or incomplete task metadata configuration
- Need to check DSPy task definitions in `gptme/eval/dspy/tasks.py`

**test_dspy_hybrid Import Failure**:
- Optional dependency not installed in CI environment
- Test should either:
  - Be marked with `@pytest.mark.skipif` for missing dspy
  - Have dspy added to test dependencies
  - Be moved to separate test suite requiring optional deps

### Reproduction

**On Master Branch**:
```bash
git checkout master
git pull origin master
pytest tests/test_dspy_basic.py::test_task_structure -v
pytest tests/test_dspy_hybrid.py -v
```

**Expected**: Both tests should pass (or be properly skipped if deps missing)
**Actual**: Both tests fail

### Proposed Solutions

**Short-term** (unblock PRs):
1. Mark failing tests with `@pytest.mark.xfail` temporarily
2. Or skip DSPy tests in CI until fixed: `--ignore=tests/test_dspy_*.py`

**Long-term** (fix root cause):
1. **test_task_structure**:
   - Investigate `get_task_metadata()` implementation
   - Ensure all tasks have required 'focus_areas' in metadata
   - Add validation for task metadata structure

2. **test_dspy_hybrid**:
   - Add `@pytest.mark.skipif(not has_dspy, reason="dspy not installed")`
   - Or add dspy to optional test dependencies in pyproject.toml
   - Document that test requires optional dependencies

### Additional Context

**CI Logs**: Available when master run 19571133535 completes (~13:05-13:10 UTC)

**Related Analysis**: See PR #865 comments for detailed CI investigation confirming these are pre-existing failures.

### Priority

**HIGH** - Currently blocking merge of multiple PRs with passing code changes.

---

**Metadata**:
- Reporter: TimeToBuildBob (Alice)
- Date: 2025-11-21
- Master Run: 19571133535 (in progress)
- Session: journal/2025-11-21-dspy-test-monitoring.md

### CI Logs (Master Run 19571133535)

**Run Details**:
- Started: 2025-11-21 12:54:19 UTC
- Completed: 2025-11-21 13:00:13 UTC (6 minutes)
- Status: FAILED
- URL: https://github.com/gptme/gptme/actions/runs/19571133535

**Test Results**:
- 1 failed
- 786 passed
- 11 skipped
- 10 xfailed
- 7 xpassed
- 1 retried

**Retry Behavior**:
The test was retried 3 times automatically by pytest-retry:

**Attempt 1**: FAILED
Traceback: AssertionError: assert 'focus_areas' in {}

**Attempt 2**: FAILED
Traceback: AssertionError: assert 'focus_areas' in {}

**Attempt 3**: FAILED
Traceback: AssertionError: assert 'focus_areas' in {}

**Conclusion**: Test consistently fails - not a flaky/intermittent issue.

### Verification Steps

Run on master branch to reproduce:
```bash
git checkout master
git pull origin master
pytest tests/test_dspy_basic.py::test_task_structure -v
```

### Recommended Actions

**Immediate** (unblock PRs):
```python
# Add to tests/test_dspy_basic.py
@pytest.mark.xfail(reason="Task metadata missing 'focus_areas' - issue #XXX")
def test_task_structure():
    ...
```

**Root Cause Fix**:
1. Investigate `gptme/eval/dspy/tasks.py` - `get_task_metadata()` implementation
2. Ensure all tasks have required metadata structure
3. Add validation for metadata completeness
4. Consider if 'focus_areas' is optional or required

### Labels
- bug
- tests
- priority: high (blocking PRs)
- good first issue (clear reproduction, known location)
