# MCP Process Isolation Investigation

**Date**: 2025-11-20
**Time**: 19:00-19:10 UTC
**Session**: Autonomous
**Duration**: ~10 minutes

## Task Selection (CASCADE)

**PRIMARY**: No actionable tasks
**SECONDARY**: PR #719 (MCP resilience) with open question about process group isolation
**SELECTED**: Investigate process group isolation for MCP servers

## Investigation Summary

### Question
Erik asked on PR #719: "Is there no way to handle this so that the user using Ctrl+C doesn't kill the MCP server?"

I had proposed implementing process group isolation by adding `start_new_session=True` to subprocess creation.

### Key Finding

**The MCP Python SDK already implements this feature!**

Location: `mcp/client/stdio/__init__.py` (line ~204)
```python
process = await anyio.open_process(
    [command, *args],
    env=env,
    stderr=errlog,
    cwd=cwd,
    start_new_session=True,  # Already implemented!
)
```

### Implications

1. **No implementation needed**: The enhancement is already in the upstream SDK
2. **PR #719 value**: Auto-restart remains valuable as defense-in-depth
3. **Issue #602 analysis**: If users still experience issues, it's likely:
   - Older SDK version without the fix
   - Platform-specific edge cases
   - Different failure mode (not Ctrl+C)

### Actions Taken

1. Investigated MCP Python SDK source code
2. Found `start_new_session=True` in subprocess creation
3. Posted detailed comment on PR #719 documenting findings
4. Recommended: Merge PR, verify SDK version, test scenario

## Technical Details

**Process Group Isolation**:
- Creates new session and process group
- Isolates subprocess from parent's SIGINT
- Prevents Ctrl+C propagation to child processes
- Standard Unix practice for daemon-like processes

**SDK Implementation**:
- Uses `anyio.open_process()` with `start_new_session=True`
- Platform-specific: Unix uses new session, Windows uses Job Objects
- Handles both process isolation and cleanup

## Outcome

- ✅ Research complete
- ✅ Finding documented in PR
- ✅ Upstream implementation confirmed
- 🎯 Better than implementing ourselves (uses maintained SDK)

## Next Steps

- Monitor PR #719 for merge
- Consider testing scenario to verify issue is resolved
- Document in knowledge base if needed
