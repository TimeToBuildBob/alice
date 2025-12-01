# Work Queue

## Current Run
Session 20251201-1300: PR maintenance. Fixed lint in PR #898, re-ran failed CI (infrastructure timeouts, not code issues). PR #890 merged, PR #896 all checks passed, PR #898 awaiting CI re-run.

## Planned Next

**WORKSPACE STATUS**: Awaiting Erik's guidance on workspace disposition (Issue #166)

**Active PRs** (can maintain regardless of workspace status):

1. **PR #898 - Fix file paths (#262)** (HIGH, CI RE-RUNNING)
   - Goal: Fix crash when cd after attaching images
   - Status: Lint fixed, CI re-running after timeout
   - Link: https://github.com/gptme/gptme/pull/898

2. **PR #896 - Fix Groq/DeepSeek (#375)** (HIGH, READY FOR REVIEW)
   - Goal: Handle mixed content types in message transformation
   - Status: ✅ All 10 CI checks passed
   - Link: https://github.com/gptme/gptme/pull/896

3. **PR #723 - Native Web Search (#492)** (MEDIUM, AWAITING REVIEW)
   - Goal: Add Anthropic native web search support
   - Status: ✅ All CI passed, awaiting maintainer review
   - Link: https://github.com/gptme/gptme/pull/723

4. **PR #885 - Workspace Tool** (LOW, AWAITING REVIEW)
   - Goal: Add workspace navigation helper tool
   - Status: ✅ All CI passed, awaiting maintainer review
   - Link: https://github.com/gptme/gptme/pull/885

**Blocked Until Workspace Resolved**:
- Complete Initial Agent Setup (requires interactive session)

## Recently Completed

- ✅ **PR #890 - Diagnostic Logging Merged** (2025-12-01) - Merged to master
- ✅ **PR #897 - Docs Dependencies Merged** (2025-12-01) - Merged to master
- ✅ **PR #894 - Rich Markup Escaping Merged** (2025-12-01) - Merged to master
- ✅ **PR #898 Lint Fix** (2025-12-01 13:00 UTC) - Fixed import order in test_message.py
- ✅ **PR #896 CI Re-run** (2025-12-01 13:00 UTC) - All checks now passing

## Last Updated
2025-12-01 13:20 UTC
