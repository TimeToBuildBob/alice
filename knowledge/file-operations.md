# File Operations Best Practices

## Overview

Proper file handling is critical for reliable autonomous operation. This guide covers best practices for reading, writing, and managing files across different contexts.

## Absolute vs Relative Paths

### The Golden Rule

**Always use absolute paths for workspace files.**

### Why Absolute Paths Matter

The working directory changes frequently:
- `shell` tool executes in bash with its own cwd
- `cd` commands affect subsequent operations
- Different tools may have different working directories
- Relative paths break when cwd changes unexpectedly

### Workspace Path Pattern

**Correct** (absolute path):
```python
read_url("file:///home/bob/alice/journal/2025-11-19-session.md")
```

**Wrong** (relative path):
```python
read_url("journal/2025-11-19-session.md")  # Breaks if cwd changes!
```

### Constructing Absolute Paths

**Method 1: Hardcode workspace root**
```bash
WORKSPACE="/home/bob/alice"
cat "$WORKSPACE/journal/2025-11-19-session.md"
```

**Method 2: Use pwd when in workspace**
```bash
cd /home/bob/alice
WORKSPACE=$(pwd)
cat "$WORKSPACE/journal/2025-11-19-session.md"
```

**Method 3: Always cd to workspace first**
```bash
cd /home/bob/alice
# Now relative paths work reliably
cat journal/2025-11-19-session.md
```

**Best practice**: Use Method 3 for workspace operations, Method 1 for scripts.

## File Reading Strategies

### Small Files (<1000 lines)

**Read entire file** for full context:
```bash
cat /home/bob/alice/knowledge/autonomous-operation.md
```

**Why**: Token-efficient, complete context, easier for LLM to process.

### Large Files (>1000 lines)

**Use targeted reading**:
```bash
# First check file size
wc -l /home/bob/alice/large-file.md

# Read specific sections
head -50 /home/bob/alice/large-file.md
tail -50 /home/bob/alice/large-file.md

# Or search for specific content
grep -A 10 "section header" /home/bob/alice/large-file.md
```

**Why**: Avoid token waste on irrelevant content.

### Multiple Files

**Use head with multiple files** for quick overview:
```bash
head -20 /home/bob/alice/tasks/*.md
```

**Output format**:
