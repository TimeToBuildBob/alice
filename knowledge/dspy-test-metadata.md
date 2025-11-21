# DSPy Test Metadata Requirements

## Overview

DSPy tests in gptme require explicit task metadata registration when using tasks with varying complexity parameters. This document explains the failure mode and fix pattern.

## The Problem

### Symptom
```python
AssertionError: assert 'focus_areas' in {}
KeyError: 'Task(complexity=2)'
```

Tests fail with missing task metadata when DSPy evaluations use tasks with different complexity levels.

### Root Cause

DSPy's evaluation system expects task metadata to be registered in `TestConfig` before running evaluations. When tests create tasks with varying complexity:

```python
tasks = [Task(complexity=i) for i in [1, 2, 3]]
```

Each unique task needs its metadata registered, or DSPy cannot access task properties during evaluation.

## The Fix Pattern

### Before (Incorrect)
```python
def test_dspy_with_complexity():
    tasks = [Task(complexity=i) for i in [1, 2, 3]]
    # Tasks not registered - will fail!
    evaluate_tasks(tasks)
```

### After (Correct)
```python
def test_dspy_with_complexity():
    tasks = [Task(complexity=i) for i in [1, 2, 3]]

    # Register metadata for all task variations
    for task in tasks:
        TestConfig.from_task(task)

    # Now evaluation works
    evaluate_tasks(tasks)
```

## Implementation Details

### Why TestConfig.from_task()?

The `TestConfig.from_task()` method:
1. Extracts task metadata (instructions, focus areas, etc.)
2. Registers it in DSPy's internal metadata store
3. Makes metadata available during evaluation

### Where to Apply

Apply this pattern in any test that:
- Uses DSPy evaluation (`evaluate`, `Evaluate` class)
- Creates tasks with varying parameters
- Encounters "assert 'focus_areas' in {}" errors

### Common Scenarios

**Multiple complexity levels**:
```python
tasks = [Task(complexity=i) for i in [1, 2, 3]]
for task in tasks:
    TestConfig.from_task(task)
```

**Dynamic task generation**:
```python
tasks = generate_test_tasks(count=5)
for task in tasks:
    TestConfig.from_task(task)
```

**Parameterized tests**:
```python
@pytest.mark.parametrize("complexity", [1, 2, 3])
def test_task(complexity):
    task = Task(complexity=complexity)
    TestConfig.from_task(task)  # Register before use
    evaluate([task])
```

## Verification

### Test Success Indicators
- No KeyError for task lookups
- All task metadata accessible during evaluation
- Tests pass consistently across complexity levels

### Quick Check
```bash
# Run DSPy tests
pytest tests/test_dspy*.py -v

# Should see all assertions pass
# No "assert 'focus_areas' in {}" errors
```

## Related

- **PR #867**: Implemented this fix for complexity test tasks
- **CI Investigation**: [ci-failure-investigation.md](./ci-failure-investigation.md)
- **Test Structure**: See `tests/test_dspy*.py` for examples

## History

- **2025-11-21**: Discovered root cause and implemented fix in PR #867
- **Issue #866**: Comprehensive analysis of DSPy test failures
