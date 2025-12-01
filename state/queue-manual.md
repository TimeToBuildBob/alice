# Work Queue

## Current Run
Session 20251201-1700: PR #902 thread safety fixes. Added buffer locks, job locks, size limits, and proper test cleanup. All CI passed.

## Planned Next

**WORKSPACE STATUS**: Awaiting Erik's guidance on workspace disposition (Issue #166)

**Active PRs**:

1. **PR #902 - Background Shell Jobs (#576)** (HIGH, AWAITING REVIEW)
   - Goal: Add background job support for long-running commands
   - Status: ✅ All CI passed, thread safety fixes addressed
   - Link: https://github.com/gptme/gptme/pull/902

2. **PR #723 - Native Web Search (#492)** (MEDIUM, AWAITING REVIEW)
   - Goal: Add Anthropic native web search support
   - Status: ✅ All CI passed, awaiting maintainer review
   - Link: https://github.com/gptme/gptme/pull/723

3. **PR #885 - Workspace Tool** (LOW, AWAITING REVIEW)
   - Goal: Add workspace navigation helper tool
   - Status: ✅ All CI passed, awaiting maintainer review
   - Link: https://github.com/gptme/gptme/pull/885

**Blocked Until Workspace Resolved**:
- Complete Initial Agent Setup (requires interactive session)

## Recently Completed

- ✅ **PR #902 Thread Safety** (2025-12-01 17:14 UTC) - Buffer locks, job locks, size limits
- ✅ **PR #901 - Tmux Wait Command Merged** (2025-12-01) - Monitor long-running commands
- ✅ **PR #898 - Fix file paths Merged** (2025-12-01) - Fixed crash when cd after attaching images
- ✅ **PR #896 - Fix Groq/DeepSeek Merged** (2025-12-01) - Handle mixed content types
- ✅ **PR #890 - Diagnostic Logging Merged** (2025-12-01)
- ✅ **PR #897 - Docs Dependencies Merged** (2025-12-01)
- ✅ **PR #894 - Rich Markup Escaping Merged** (2025-12-01)

## Last Updated
2025-12-01 17:14 UTC
