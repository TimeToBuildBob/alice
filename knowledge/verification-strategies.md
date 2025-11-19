# Verification Strategies

This guide documents strategies for verifying work is correct before marking tasks complete or ending sessions.

## Why Verification Matters

Proper verification ensures:
- Work actually solves the intended problem
- No regressions or breaking changes
- Quality standards are met
- Confidence in autonomous operation
- Reduced need for rework

## Verification Levels

### 1. Syntax Verification

**Purpose**: Ensure code/config is syntactically correct.

**Methods**:
```bash
# Python syntax
python -m py_compile file.py

# JSON syntax
jq . file.json

# YAML syntax
python -c "import yaml; yaml.safe_load(open('file.yaml'))"

# Markdown links (if tool available)
markdown-link-check file.md
```

**When to use**: Always for code/config changes.

### 2. Functional Verification

**Purpose**: Ensure code actually works as intended.

**Methods**:
```bash
# Run unit tests
pytest tests/

# Run specific test
pytest tests/test_feature.py::test_function

# Run integration tests
python -m unittest discover

# Manual testing
python script.py --test-arg
```

**When to use**: For any code changes, especially new features.

### 3. Integration Verification

**Purpose**: Ensure changes work with the rest of the system.

**Methods**:
```bash
# CI checks
gh pr checks <pr_url>

# Build project
make build
npm run build
cargo build

# Run full test suite
make test
npm test
```

**When to use**: For changes affecting multiple components.

### 4. Manual Verification

**Purpose**: Human review of output/behavior.

**Methods**:
- Review generated output
- Check documentation rendering
- Verify UI changes
- Test user workflows
- Read through code changes

**When to use**: For documentation, UI, or complex logic.

## Verification by Work Type

### Code Changes

**Essential checks**:
1. ✅ Syntax valid (linting, compilation)
2. ✅ Unit tests pass
3. ✅ Integration tests pass
4. ✅ CI checks green
5. ✅ No obvious bugs in review

**Commands**:
```bash
# Pre-commit checks
pre-commit run --all-files

# Run tests
pytest
npm test
cargo test

# Check CI
gh pr checks <pr_url>

# Review changes
git diff
```

### Documentation Changes

**Essential checks**:
1. ✅ Markdown syntax valid
2. ✅ Links work
3. ✅ Examples are correct
4. ✅ Formatting renders properly
5. ✅ Content is accurate

**Commands**:
```bash
# Check markdown
markdownlint file.md

# Preview rendering (if available)
mdcat file.md
grip file.md

# Verify links
markdown-link-check file.md

# Manual review
cat file.md
```

### Configuration Changes

**Essential checks**:
1. ✅ Syntax valid (JSON/YAML/TOML)
2. ✅ Schema validation passes
3. ✅ System still starts/works
4. ✅ No breaking changes
5. ✅ Defaults are sensible

**Commands**:
```bash
# Validate JSON
jq . config.json

# Validate YAML
python -c "import yaml; yaml.safe_load(open('config.yaml'))"

# Test application starts
./start.sh
systemctl --user status service

# Check for errors
journalctl --user -u service --since "1 minute ago"
```

### Task/Documentation Updates

**Essential checks**:
1. ✅ Metadata valid (frontmatter)
2. ✅ Links work
3. ✅ State transitions make sense
4. ✅ Content is clear and accurate
5. ✅ Follows conventions

**Commands**:
```bash
# Validate task metadata (if task CLI available)
./scripts/tasks.py status

# Check links in file
grep "\[" file.md

# Manual review
cat file.md
```

## CI Integration

### Reading CI Status

**For GitHub PRs**:
```bash
# Quick status check (shows failed runs)
gh pr status <pr_url>

# Wait for checks to complete
gh pr checks <pr_url>

# View specific run logs
gh run view <run_id> --log-failed
```

**Interpretation**:
- ✅ All checks passed - Safe to merge
- ⚠️ Some checks pending - Wait or investigate
- ❌ Checks failed - Fix issues before proceeding

### Common CI Checks

**Python projects**:
- Linting (ruff, pylint, mypy)
- Unit tests (pytest)
- Integration tests
- Type checking (mypy)
- Code coverage

**JavaScript projects**:
- Linting (eslint)
- Unit tests (jest, vitest)
- Type checking (TypeScript)
- Build verification

**Documentation**:
- Markdown linting
- Link checking
- Spelling
- Build/rendering

## Pre-Commit Hooks

**Purpose**: Catch issues before committing.

**Setup**:
```bash
# Install pre-commit
pipx install pre-commit

# Install hooks
cd /home/bob/alice
pre-commit install

# Run manually
pre-commit run --all-files
```

**What they check**:
- File syntax
- Formatting
- Trailing whitespace
- Large files
- Merge conflicts
- Custom validations

## Verification Checklist by Session Type

### Autonomous Session

Before using `complete` tool:

- [ ] Primary work completed
- [ ] Changes verified (tests/CI/manual)
- [ ] Documentation updated
- [ ] Journal entry created
- [ ] Work queue updated
- [ ] Changes committed and pushed
- [ ] No unexpected errors in logs

### Feature Implementation

- [ ] Feature works as intended
- [ ] Unit tests written and passing
- [ ] Integration tests passing
- [ ] CI checks green
- [ ] Documentation updated
- [ ] Examples added
- [ ] Edge cases considered

### Bug Fix

- [ ] Bug no longer reproduces
- [ ] Root cause understood
- [ ] Fix doesn't break other functionality
- [ ] Test added to prevent regression
- [ ] CI checks pass
- [ ] Related documentation updated

### Documentation Work

- [ ] Content is accurate
- [ ] Examples work
- [ ] Links are valid
- [ ] Formatting is correct
- [ ] Follows conventions
- [ ] Related docs updated

## Common Verification Mistakes

### ❌ Assuming Success

**Problem**: Assuming changes worked without checking.

**Solution**: Always verify, especially for:
- First time trying something
- Complex changes
- Multiple file edits
- Configuration changes

### ❌ Partial Testing

**Problem**: Only testing the happy path.

**Solution**: Test edge cases:
```bash
# Test normal case
python script.py --input normal.txt

# Test edge cases
python script.py --input empty.txt
python script.py --input large.txt
python script.py --input malformed.txt
```

### ❌ Ignoring Warnings

**Problem**: Treating warnings as ignorable.

**Solution**: Investigate warnings:
```bash
# Don't ignore warnings in output
pytest  # Check for DeprecationWarning
npm run build  # Check for warnings
```

### ❌ Not Checking CI

**Problem**: Committing without checking if CI will pass.

**Solution**: Run local checks first:
```bash
# Run pre-commit hooks
pre-commit run --all-files

# Run tests locally
pytest

# Check build
make build
```

## Verification Tools

### Available in Workspace

```bash
# Git operations
git status
git diff
git log

# File operations
cat, grep, find, ls

# Python
python -m py_compile
python -m pytest
python -m unittest

# Shell
shellcheck script.sh

# JSON/YAML
jq
python -c "import yaml..."
```

### External Tools (via gh)

```bash
# GitHub CLI
gh pr status
gh pr checks
gh run view
gh issue list
```

### MCP Servers (when available)

```bash
# Browser for manual verification
read_url(url)
screenshot_url(url)

# GitHub for PR review
# (via MCP github server)
```

## Best Practices

### 1. Verify Before Committing

Always check your changes before committing:
```bash
# Review changes
git diff

# Test changes
pytest
npm test

# Verify syntax
python -m py_compile *.py
```

### 2. Automate What You Can

Use tools to automate verification:
```bash
# Pre-commit hooks
pre-commit install

# CI checks
# (automatically run on push)

# Test frameworks
pytest --cov
npm test
```

### 3. Document Verification Steps

In task files, document how to verify:
```markdown
## Verification

To verify this task:
1. Run tests: `pytest tests/test_feature.py`
2. Check CI: `gh pr status <pr_url>`
3. Manual test: `python script.py --test`
4. Review output in `output/`
```

### 4. Use Multiple Verification Methods

Don't rely on single verification:
```bash
# Multiple checks
pytest                          # Unit tests
pytest --cov                    # Coverage
pre-commit run --all-files      # Linting
gh pr checks <pr_url>          # CI
python script.py --dry-run     # Manual
```

### 5. Verify the Verification

Make sure your verification is actually working:
```bash
# Test that tests fail when they should
# (temporarily break code, run tests, see failure)

# Check CI is actually running
gh run list

# Verify pre-commit hooks are installed
pre-commit run --all-files
```

## Quick Reference

### Before Committing

```bash
git status                      # Review changes
git diff                        # Check diff
pre-commit run --all-files     # Run checks
pytest                          # Run tests
```

### Before Marking Task Done

- [ ] Success criteria met
- [ ] Tests pass
- [ ] CI checks pass (if applicable)
- [ ] Documentation updated
- [ ] Manual verification completed

### Before Ending Session

- [ ] Primary work verified
- [ ] Journal entry complete
- [ ] Work queue updated
- [ ] Changes committed and pushed
- [ ] No errors in logs

## Related

- Task State Management (task-state-management.md) - When to mark tasks done
- Autonomous Operation Patterns (autonomous-operation-patterns.md) - Verification in autonomous sessions
- TASKS.md - Task completion criteria
