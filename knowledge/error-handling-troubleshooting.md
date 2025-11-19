# Error Handling and Troubleshooting

This guide documents common errors, diagnostic strategies, and solutions for autonomous operation.

## Diagnostic Approach

When encountering an error, follow this systematic approach:

### 1. Read the Error Message

**Purpose**: Understand what actually failed.

```bash
# ✅ Read full error output
command 2>&1 | tail -50

# ❌ Don't ignore error details
command > /dev/null 2>&1  # Hides errors
```

**Key information**:
- Error type/name
- File/line number
- Stack trace
- Context about what was attempted

### 2. Check Recent Changes

**Purpose**: Identify what changed that might have caused the issue.

```bash
# Check recent commits
git log --oneline -5

# See what changed
git diff HEAD~1

# Check file history
git log -p -- path/to/file
```

### 3. Verify Environment

**Purpose**: Ensure prerequisites and dependencies are correct.

```bash
# Check Python environment
which python
python --version

# Check PATH
echo $PATH

# Check installed packages
pip list | grep package

# Check system info
uname -a
```

### 4. Test Incrementally

**Purpose**: Isolate the specific cause.

```bash
# Test simple case first
command --simple

# Add complexity gradually
command --simple --extra-arg

# Test components individually
python -c "import module"
```

### 5. Review Documentation

**Purpose**: Check if there's a known solution.

```bash
# Search workspace knowledge base
git grep -i "error_message" knowledge/

# Search lessons
git grep -i "error_message" lessons/

# Check tool documentation
command --help
```

## Common Error Categories

### File Not Found Errors

**Symptoms**:
