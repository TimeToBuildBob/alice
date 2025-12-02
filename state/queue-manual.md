# Work Queue

## Current Run
Session 20251202-0900: Working on PR #907 - implemented Erik's feedback with new interrupt/queue dialog approach. CI running.

## Planned Next

**WORKSPACE STATUS**: Awaiting Erik's review of open PRs + PR #907 feedback

**Active PRs Awaiting Review** (All CI Passing/Running):

1. **PR #907 - Prompt Queueing (#569)** (HIGH, CI RUNNING)
   - Goal: Allow queueing prompts while agent is working
   - Status: ⏳ Implemented new approach per Erik's feedback, CI running
   - Changes: Added interrupt/queue/cancel dialog during generation
   - Link: https://github.com/gptme/gptme/pull/907
   - Needs: Erik's review of new implementation

2. **PR #902 - Background Shell Jobs (#576)** (HIGH, AWAITING REVIEW)
   - Goal: Add background job support for long-running commands
   - Status: ✅ All CI passed
   - Link: https://github.com/gptme/gptme/pull/902

3. **PR #906 - Fix Shell Output Mixing (#408)** (HIGH, AWAITING REVIEW)
   - Goal: Fix unreliability of shell tool output mixing between commands
   - Status: ✅ All CI passed
   - Link: https://github.com/gptme/gptme/pull/906

**Additional PRs Awaiting Review**:

4. **PR #909 - Fix Config Assertions (#908)** (MEDIUM)
   - Goal: Fix config loading failure when no user config exists
   - Status: ⏳ CI re-triggered (infrastructure failure)
   - Link: https://github.com/gptme/gptme/pull/909

5. **PR #903 - Nested Codeblocks (#111)** (MEDIUM)
   - Status: ✅ All CI passed
   - Link: https://github.com/gptme/gptme/pull/903

6. **PR #904 - Search Bot Detection (#492)** (MEDIUM)
   - Status: ✅ All CI passed
   - Link: https://github.com/gptme/gptme/pull/904

7. **PR #905 - GitHub Bot Docker (#305)** (LOW)
   - Status: ✅ All CI passed
   - Link: https://github.com/gptme/gptme/pull/905

**Blocked Until Workspace Resolved**:
- Complete Initial Agent Setup (requires interactive session)

## Recently Completed

- ✅ **PR #907 Redesign** (2025-12-02 09:15 UTC) - Implemented new interrupt/queue dialog
- ✅ **PR #909 Created** (2025-12-02 06:08 UTC) - Fix config assertions
- ✅ **PR #906 CI Fixed** (2025-12-01 19:40 UTC) - Pre-drain timing
- ✅ **PR #907 CI Fixed** (2025-12-01 19:43 UTC) - Lint fixes
- ✅ **PR #901 Merged** (2025-12-01) - Tmux wait command
- ✅ **PR #898 Merged** (2025-12-01) - Fix file paths

## Last Updated
2025-12-02 09:18 UTC
