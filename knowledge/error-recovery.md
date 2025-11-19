# Error Recovery and Troubleshooting

## Overview

Autonomous sessions will encounter errors. This guide provides systematic approaches to identifying, diagnosing, and recovering from common failure modes.

## Error Classification

### Recoverable Errors
Can be fixed within the current session:
- Missing dependencies
- Configuration issues
- Syntax errors
- File permission problems
- Network timeouts (retry-able)

### Blocking Errors
Require external action:
- API credentials invalid
- External service down
- Requires human decision
- Needs system-level access

## Recovery Workflow

### 1. Capture Error Details
```bash
# Run command, capture full output
command 2>&1 | tee error.log

# Get error code
echo $?

# Check system logs if relevant
journalctl --user --since "5 minutes ago" --no-pager -o cat | tail -50
```

**What to capture**:
- Complete error message
- Stack trace if available
- Command that failed
- Return code
- Context (what you were trying to do)

### 2. Identify Error Type

**Pattern matching**:
