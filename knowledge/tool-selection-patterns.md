# Tool Selection Patterns

This guide helps you choose the right tool for common tasks in gptme.

## File Operations

### When to use `save`

**Purpose**: Create new file or completely overwrite existing file.

**Use when**:
- Creating a new file
- Content is completely different from existing file
- Rewriting entire file is clearer than patching
- File is small (<100 lines)

**Example**:
```python
# Creating new file
save path/to/new-file.py

# Completely rewriting small file
save path/to/config.json
```

### When to use `patch`

**Purpose**: Make targeted edits to existing files.

**Use when**:
- Editing specific sections of a file
- Preserving comments and structure
- File is large (>100 lines)
- Making multiple small changes
- Only changing a few lines

**Example**:
```python
# Edit specific function in large file
patch src/main.py
<<<<<<< ORIGINAL
def old_function():
    return "old"
=======
def old_function():
    return "new"
>>>>>>> UPDATED
```

**Benefits**:
- Preserves comments
- Shows exactly what changed
- Safer for large files
- Less token usage for small edits

### When to use `append`

**Purpose**: Add content to end of existing file.

**Use when**:
- Adding new entries to logs
- Appending to journal files
- Adding items to lists
- Content is purely additive

**Example**:
```markdown
# Add to journal
append journal/2025-11-19.md

## New Section
Content here...
```

### When to use `read`/`cat`

**Purpose**: View file contents.

**Use when**:
- Need to see current content before editing
- Checking file structure
- Verifying changes
- File is reasonably sized (<1000 lines)

**Pattern**:
```bash
# For workspace files
cat /home/bob/alice/path/to/file

# For small files
cat file.txt

# For large files (filter first)
head -100 large-file.txt
tail -100 large-file.txt
grep "pattern" large-file.txt
```

## Code Execution

### When to use `shell`

**Purpose**: Execute shell commands, manage system operations.

**Use when**:
- Running git commands
- Managing files and directories
- Installing packages
- Running build tools
- System administration

**Example**:
```bash
# Git operations
git status
git commit -m "message"

# File operations
ls -la
mkdir directory

# Package management
pip install package
```

### When to use `ipython`

**Purpose**: Execute Python code, use specialized functions.

**Use when**:
- Running Python scripts/calculations
- Using browser functions (read_url, search, screenshot_url)
- Using chat functions (list_chats, search_chats, read_chat)
- Testing Python code
- Data analysis

**Example**:
```python
# Browser operations
read_url('https://example.com')
search('query', 'google')

# Chat operations
search_chats('topic')

# Python calculations
import numpy as np
np.mean([1, 2, 3, 4, 5])
```

## Git Operations

### When to use `shell` for git

**Use when**:
- Basic git operations (status, add, commit, push)
- Working with workspace repository
- Simple git queries

**Example**:
```bash
git status
git add file.txt
git commit -m "docs: update"
git push
git log --oneline -10
```

### When to use `gh` tool

**Use when**:
- Reading PRs with full context (reviews, comments, code)
- Checking CI status
- Waiting for CI checks to complete
- Creating/managing GitHub issues
- Managing repositories

**Example**:
```bash
# Read PR with full context
gh pr view https://github.com/owner/repo/pull/123

# Check CI status
gh pr status https://github.com/owner/repo/pull/123

# Wait for CI
gh pr checks https://github.com/owner/repo/pull/123
```

**Note**: For PRs, always use `gh pr view` to get full context including review comments and suggestions, not just `gh pr view --comments`.

## Documentation

### When to use `save` for docs

**Use when**:
- Creating new documentation file
- Completely restructuring existing doc
- Doc is short and simple

### When to use `patch` for docs

**Use when**:
- Adding section to existing doc
- Updating specific parts
- Preserving structure and formatting

### When to use `append` for docs

**Use when**:
- Adding to journal entries
- Adding to changelog
- Adding to lists (tasks, notes)

## Search Operations

### When to use `git grep`

**Purpose**: Search files tracked by git (respects .gitignore).

**Use when**:
- Searching workspace content
- Finding files or patterns
- Need to respect .gitignore

**Example**:
```bash
# Find files containing term
git grep -li "pattern"

# Show matching lines
git grep -i "pattern"
```

### When to use regular `grep`

**Use when**:
- Filtering command output
- Searching specific files
- Pipeline operations

**Example**:
```bash
# Filter command output
ps aux | grep python
ls -la | grep ".py"
```

### When to avoid `find`

**Problem**: `find` doesn't respect .gitignore by default.

**Instead use**:
```bash
# Use git ls-files instead
git ls-files | grep pattern

# Or git grep for content search
git grep -l "pattern"
```

## Tool Selection Decision Tree
