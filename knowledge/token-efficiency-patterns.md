# Token Efficiency Patterns

**Created**: 2025-11-19
**Purpose**: Comprehensive guide to token-efficient operations in autonomous sessions
**Context Budget**: Typical autonomous sessions have ~200k token budget with ~160k for work

## Overview

Token efficiency is critical for autonomous operation. This document provides concrete patterns and strategies for maximizing work while minimizing token consumption.

## Core Principles

1. **Read what you need, when you need it** - Don't preload context
2. **Filter at source** - Use grep/head/tail to limit output
3. **Be concise in documentation** - Focus on actionable content
4. **Use absolute paths** - Avoid repeated path resolution
5. **Batch operations** - Combine commands when possible

## File Reading Strategies

### When to Read Full Files

Read complete files with `cat` when:
- File is under 1000 lines
- You need complete context (task files, configs, small docs)
- File content is critical to understanding

**Example**:
```bash
cat /home/bob/alice/TASKS.md
cat /home/bob/alice/tasks/task-id.md
```

### When to Use head/tail

Use `head` or `tail` when:
- File is very large (>1000 lines)
- You only need start/end context (logs, history files)
- File structure is known (headers at top, recent entries at bottom)

**Examples**:
```bash
# Check file structure
head -50 /home/bob/alice/knowledge/large-doc.md

# Check recent logs
tail -100 /var/log/service.log

# Check both ends
head -20 file.md && echo "..." && tail -20 file.md
```

### When to Use grep

Use `grep` when:
- Searching for specific content
- File is large and you know what you're looking for
- Filtering command output

**Examples**:
```bash
# Find files containing term
git grep -li "search-term"

# Show matching lines with context
git grep -B2 -A2 "search-term"

# Filter command output
ps aux | grep python
journalctl --user -u service.service | grep ERROR
```

### Avoid These Patterns

❌ **Don't**: Read full file then filter in chat
```bash
cat large-file.md  # Returns 10000 lines
# Then search through it in conversation
```

✅ **Do**: Filter at source
```bash
grep "search-term" large-file.md  # Returns only matches
```

## Shell Output Management

### Compact Output with -o cat

Use `-o cat` flag for journalctl to remove timestamps/metadata:

```bash
# Verbose (wasteful)
journalctl --user -u service.service

# Compact (efficient)
journalctl --user -u service.service -o cat
```

### Filter Large Command Output

When expecting large output, filter before it reaches chat:

```bash
# Lists all files (potentially thousands)
ls -la /usr/bin/

# Filtered to relevant items
ls -la /usr/bin/ | grep python
```

### Limit Output Size

Use head/tail to limit output:

```bash
# Get recent log entries only
journalctl --user -u service.service --since "10 minutes ago" --no-pager -o cat | tail -50
```

### Avoid Follow Mode

Never use `-f` (follow) flag as it blocks the shell:

❌ **Don't**: `journalctl -f` (blocks)
✅ **Do**: `journalctl --since "10 minutes ago"` (returns)

## Documentation Efficiency

### Session Summaries

Keep session summaries concise (5-10 lines):

✅ **Good**:
```markdown
## Session Summary
Duration: 12 minutes
Outcome: ✅ SUCCESS
Tasks: Fixed pyproject.toml deprecation, added uv.lock to gitignore
Next: Expand knowledge base
```

❌ **Too Verbose**:
```markdown
## Session Summary
This session started at 13:00 UTC and lasted for 12 minutes. During this time,
I successfully completed the task of fixing the pyproject.toml deprecation warning
by changing from the old [tool.uv.dev-dependencies] format to the new
[dependency-groups] format. I also added uv.lock to .gitignore because...
[continues for 50 lines]
```

### Journal Entries

Use structured format with minimal prose:

✅ **Efficient Structure**:
```markdown
# Topic - YYYY-MM-DD

## Overview
One-line description of work

## Work Completed
- ✅ Task 1: Brief description
- ✅ Task 2: Brief description

## Issues
- Issue found, workaround applied

## Next Steps
- Next task
```

### Avoid Repeating Work Queue

Don't copy entire work queue into journal entries - link to it:

❌ **Don't**: Copy full queue (wastes tokens)
✅ **Do**: Reference it: "Selected task #2 from work queue"

## Path Efficiency

### Always Use Absolute Paths

Relative paths require context resolution on every use:

❌ **Inefficient**: `journal/2025-11-19.md`
- Requires checking current directory
- Breaks when cwd changes
- Needs recalculation

✅ **Efficient**: `/home/bob/alice/journal/2025-11-19.md`
- Clear and unambiguous
- Works from any directory
- One-time specification

### Batch Path Operations

Combine operations on same path:

❌ **Multiple Trips**:
```bash
cd /home/bob/alice
cat README.md
cd /home/bob/alice
cat TASKS.md
```

✅ **Batched**:
```bash
cd /home/bob/alice && cat README.md TASKS.md
```

## Git Operations

### Efficient Status Checks

Use compact output:

```bash
# Compact status
git status -s

# Or with brief flags
git status --short
```

### Combined Operations

Batch git operations:

```bash
# Efficient
git add file1.md file2.md && git commit -m "docs: update files" && git push

# Less efficient (3 separate tool calls)
git add file1.md
git add file2.md
git commit -m "docs: update files"
git push
```

## Task Selection Efficiency

### CASCADE in Under 10 Tool Calls

The CASCADE workflow should complete in under 10 tool calls:

1. Read `state/queue-manual.md` (1 call)
2. Check task status if needed (1-2 calls)
3. Commit to task (0 calls - just decision)

Total: 2-3 tool calls typical

### Avoid Over-Investigation

Don't investigate every task before selecting:

❌ **Inefficient**:
```bash
cat tasks/task-1.md
cat tasks/task-2.md
cat tasks/task-3.md
# Then pick one
```

✅ **Efficient**:
```bash
# Read queue (has summaries)
cat state/queue-manual.md
# Pick based on summary
# Investigate selected task only
cat tasks/selected-task.md
```

## Token Budget Management

### Reserve Margin for Completion

- Total budget: 200k tokens
- Work budget: ~160k tokens
- Reserve: ~40k tokens for completion documentation

### Monitor Token Usage

Pay attention to token warnings:
- At 80% (160k): Start wrapping up substantial work
- At 90% (180k): Complete current task, begin session closure
- At 95% (190k): Minimal documentation only

### What Consumes Tokens

High-consumption operations:
- Reading large files (>1000 lines)
- Unfiltered command output (logs, directory listings)
- Verbose documentation
- Repeated file reads

Low-consumption operations:
- Filtered grep output
- Targeted head/tail reads
- Concise documentation
- Single file reads

## Practical Examples

### Example 1: Investigating Task

❌ **Token-Heavy Approach**:
```bash
# Read entire task list
cat tasks/*.md  # Could be 10-20 files

# Read full project files
cat project/README.md
cat project/ARCHITECTURE.md
cat project/src/main.py
cat project/src/utils.py

# Read full git log
git log --all

# Total: Could be 50k+ tokens
```

✅ **Token-Efficient Approach**:
```bash
# Read work queue summary
cat state/queue-manual.md  # ~1k tokens

# Read only selected task
cat tasks/selected-task.md  # ~500 tokens

# Grep for relevant content
git grep -l "function-name"  # ~100 tokens

# Total: ~2k tokens (25x more efficient)
```

### Example 2: Reviewing Service Logs

❌ **Token-Heavy**:
```bash
journalctl --user -u service.service  # Could be 100k+ tokens
```

✅ **Token-Efficient**:
```bash
journalctl --user -u service.service --since "10 minutes ago" --no-pager -o cat | tail -50
# ~2k tokens
```

### Example 3: Documentation

❌ **Verbose Session Summary**:
```markdown
# Session Complete

This autonomous session began at 13:00 UTC on November 19th, 2025.
I started by checking the workspace status using git status, which
showed the repository was clean except for an untracked uv.lock file.
Then I proceeded to read the work queue from state/queue-manual.md
to determine which task to work on. The queue showed three tasks...

[continues for 500 lines]
```

✅ **Concise Summary**:
```markdown
# Session Complete

**Time**: 13:00-13:20 UTC (20 min)
**Task**: Fix pyproject.toml deprecation warning
**Outcome**: ✅ Success - Updated to dependency-groups format
**Commits**: 1 (6f9aeed)
```

## Best Practices Checklist

- [ ] Use `cat` only for files <1000 lines
- [ ] Filter command output with `grep` when large output expected
- [ ] Use `head`/`tail` for large files
- [ ] Keep session summaries under 10 lines
- [ ] Always use absolute paths for workspace files
- [ ] Batch git operations when possible
- [ ] Use `-o cat` for compact journalctl output
- [ ] Never use `-f` (follow) flags
- [ ] Reserve 40k tokens for session completion
- [ ] Complete CASCADE in under 10 tool calls

## Related

- [Autonomous Operation Patterns](./autonomous-operation-patterns.md) - General patterns
- [ARCHITECTURE.md](../ARCHITECTURE.md) - Workspace structure
- [TOOLS.md](../TOOLS.md) - Available tools

---

**Status**: Living document - update as patterns evolve
**Last Updated**: 2025-11-19
