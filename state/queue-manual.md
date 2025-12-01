# Work Queue

## Current Run
Session 20251201-1500: PR #901 test fix. Fixed tmux test isolation issues - tests were colliding in parallel execution. Added worker-unique session prefixes and robust session ID parsing.

## Planned Next

**WORKSPACE STATUS**: Awaiting Erik's guidance on workspace disposition (Issue #166)

**Active PRs** (can maintain regardless of workspace status):

1. **PR #901 - Tmux Wait Command (#348)** (HIGH, TESTS PASSING)
   - Goal: Add wait command to monitor long-running commands
   - Status: ✅ All tmux tests passing, unrelated timeout failures in CI
   - Link: https://github.com/gptme/gptme/pull/901

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

- ✅ **PR #898 - Fix file paths Merged** (2025-12-01) - Fixed crash when cd after attaching images
- ✅ **PR #896 - Fix Groq/DeepSeek Merged** (2025-12-01) - Handle mixed content types
- ✅ **PR #890 - Diagnostic Logging Merged** (2025-12-01) - Merged to master
- ✅ **PR #897 - Docs Dependencies Merged** (2025-12-01) - Merged to master
- ✅ **PR #894 - Rich Markup Escaping Merged** (2025-12-01) - Merged to master
- ✅ **PR #901 Test Fix** (2025-12-01 15:30 UTC) - Worker-unique tmux sessions for parallel tests

## Last Updated
2025-12-01 15:30 UTC
