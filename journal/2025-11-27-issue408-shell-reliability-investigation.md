# Issue #408 Investigation: Shell Tool Output Confusion

**Date**: 2025-11-27
**Issue**: https://github.com/ErikBjare/gptme/issues/408
**Status**: Investigation complete, solutions proposed

## Problem Statement

Shell tool occasionally returns output from the previous command instead of current command, causing confusion for LLMs. Issue reportedly happens with interactive commands (e.g., sudo password prompts).

## Code Analysis

### Architecture

The shell tool uses a persistent bash session with:
- stdin/stdout/stderr pipes
- Delimiter-based completion detection: `ReturnCode:$? END_OF_COMMAND_OUTPUT`
- `select()` for non-blocking I/O from stdout/stderr
- stdin redirection to `/dev/null` for non-interactive commands

### Key Code Sections

**Command Execution Flow** (`_run` method):
1. Write command + delimiter to stdin
2. Use `select()` to read stdout/stderr
3. Look for delimiter pattern with return code
4. Strip output and return

**Potential Failure Points**:

1. **Delimiter Not Found**:
   - If delimiter line is malformed or interrupted
   - Output from previous command could remain in buffer

2. **Timing Issues with Interactive Commands**:
   - Commands expecting stdin input
   - stdin redirected to `/dev/null` but timing might cause confusion
   - Example from issue comment: `sudo` password prompts

3. **Output Buffering**:
   - Large outputs or slow-producing commands
   - `select()` with 2^16 byte chunks
   - Partial reads might cause boundary issues

4. **Background Processes**:
   - Commands with `&` might produce delayed output
   - Could appear in next command's output window

## Root Cause Hypothesis

**Primary Hypothesis**: Delimiter line gets corrupted or missed, causing:
- Previous command's output to remain in stdout/stderr buffers
- Next command's `select()` loop reads leftover output before new output
- LLM receives output from Command N-1 when executing Command N

**Triggering Conditions**:
- Interactive commands (sudo, password prompts)
- Commands interrupted via Ctrl+C
- Commands with stderr alongside stdout
- Very rapid command succession

## Proposed Solutions

### Solution 1: Enhanced Delimiter Detection (LOW RISK)

Add more robust delimiter matching and validation:

```python
def _run(self, command: str, ...):
    # ... existing code ...

    # Add delimiter validation
    delimiter_pattern = re.compile(
        rf"ReturnCode:(\d+)\s+{re.escape(self.delimiter)}"
    )

    for line in lines:
        # Strict delimiter matching
        match = delimiter_pattern.fullmatch(line.strip())
        if match:
            return_code = int(match.group(1))
            # Found valid delimiter - command complete
            return (return_code, stdout, stderr)
```

### Solution 2: Add Diagnostic Logging (LOW RISK - RECOMMENDED FIRST)

Add logging to detect when issue occurs:

```python
import logging
logger = logging.getLogger(__name__)

def _run(self, command: str, ...):
    # Log command start
    logger.debug(f"Running command: {command[:100]}")

    # ... existing select() loop ...

    for line in lines:
        if "ReturnCode:" in line and self.delimiter in line:
            # Log delimiter detection
            logger.debug(f"Delimiter found: {line.strip()}")

            # Check for unexpected content before delimiter
            if stdout:
                last_line = stdout[-1] if stdout else ""
                logger.debug(f"Last stdout before delimiter: {last_line[:100]}")
```

### Solution 3: Output Buffer Flushing (MEDIUM RISK)

Explicitly flush stdout/stderr before starting new command:

```python
def _run(self, command: str, ...):
    # Flush any remaining output from previous commands
    self._drain_buffers(timeout=0.1)

    # Then execute command normally
    # ... existing code ...

def _drain_buffers(self, timeout: float):
    """Drain any remaining output from stdout/stderr."""
    start = time.time()
    while time.time() - start < timeout:
        rlist, _, _ = select.select([self.stdout_fd, self.stderr_fd], [], [], 0.1)
        if not rlist:
            break
        for fd in rlist:
            os.read(fd, 2**16)  # Discard
```

### Solution 4: Command Isolation Markers (HIGH CONFIDENCE)

Add unique markers for each command to detect leakage:

```python
def _run(self, command: str, ...):
    # Generate unique command ID
    cmd_id = f"CMD_{int(time.time() * 1000)}"

    # Inject markers
    full_command = f"echo '=== START {cmd_id} ==='\n"
    full_command += f"{command}\n"
    full_command += f"echo '=== END {cmd_id} ==='\n"
    full_command += f"echo ReturnCode:$? {self.delimiter}\n"

    # Parse output and verify markers match
    # If START marker from different command appears -> BUG DETECTED
```

## Reproduction Test Suite

Created comprehensive test suite in `tests/test_shell_issue408.py` covering:
- Rapid command succession
- Large output followed by small output
- Timeout recovery
- Background processes
- Interactive command simulation

**Note**: Tests require pytest installation to run via CI.

## Recommended Action Plan

1. **IMMEDIATE** (Next session):
   - Add Solution 2 (diagnostic logging) to shell.py
   - This will help capture issue when it occurs in production

2. **SHORT TERM** (1-2 days):
   - Monitor logs during autonomous runs for delimiter anomalies
   - Gather real reproduction cases with diagnostic info

3. **MEDIUM TERM** (1 week):
   - Implement Solution 1 (enhanced delimiter detection)
   - Add Solution 3 (buffer flushing) if logs show buffer issues
   - Submit PR with logging + delimiter improvements

4. **LONG TERM** (2+ weeks):
   - Consider Solution 4 (command isolation markers) if issue persists
   - This is more invasive but provides strong isolation guarantees

## Related Issues

- Issue #684: Pipe with stdin-consuming commands (related to stdin redirection)
- Issue #703: Heredoc in compound commands (related to command parsing)

## Files Analyzed

- `gptme/tools/shell.py` - Main shell tool implementation
- `tests/test_tools_shell.py` - Existing test suite
- `tests/test_shell_issue408.py` - New reproduction tests (created)

## Time Investment

- Code analysis: 15 minutes
- Test development: 10 minutes (blocked on dependencies)
- Documentation: 10 minutes
- **Total**: ~35 minutes

## Next Steps

1. Add diagnostic logging PR
2. Monitor logs during autonomous runs
3. Wait for real-world reproduction with diagnostic data
4. Implement targeted fix based on diagnostic findings
