# Work Queue

## Current Run
Session 20251203-1900: Fixed PR #15 cache invalidation - move test passing, batch test pre-existing flaky

## Planned Next

**WORKSPACE STATUS**: 7 gptme PRs + 1 gptme-rag PR open awaiting review

**Alice's gptme PRs (All Awaiting Review)**:

1. **PR #924 - tmux overflow fix (#923)** (CI ✅)
   - Link: https://github.com/gptme/gptme/pull/924
   - Needs: Erik's review

2. **PR #917 - Speech-to-Text (#263)** (CI ✅)
   - Link: https://github.com/gptme/gptme/pull/917
   - Needs: Erik's review

3. **PR #916 - Shell Quiet Mode (#44)** (CI ✅)
   - Link: https://github.com/gptme/gptme/pull/916
   - Needs: Erik's review

4. **PR #907 - Prompt Queueing (#569)** (CI ✅)
   - Link: https://github.com/gptme/gptme/pull/907
   - Needs: Erik's review

5. **PR #902 - Background Jobs (#576)** (CI ✅)
   - Link: https://github.com/gptme/gptme/pull/902
   - Needs: Erik's review

6. **PR #885 - Workspace Navigation** (CI ✅)
   - Link: https://github.com/gptme/gptme/pull/885
   - Needs: Erik's review

7. **PR #723 - Anthropic Web Search** (CI ✅)
   - Link: https://github.com/gptme/gptme/pull/723
   - Needs: Erik's review

**gptme-rag PR**:

8. **PR #15 - Flaky watcher test fix** (CI: 2/3 ✅, 1 flaky)
   - Link: https://github.com/gptme/gptme-rag/pull/15
   - Fixed cache invalidation: test_file_watcher_move now passing
   - Pre-existing test_file_watcher_batch_updates intermittently failing
   - Needs: Erik's review

**Blocked Until Resolved**:
- Complete Initial Agent Setup (requires interactive session)
- All "status: ready" issues have PRs, waiting for reviews

## Recently Completed

- ✅ **gptme-rag PR #14 Merged** - chromadb 1.x upgrade with compatibility fixes
- ✅ **gptme-rag PR #15 Updated** - Fixed cache invalidation bug for flaky watcher test
- ✅ **2 Lessons Added** - CI failure triage, blocked task selection
- ✅ **PR #922 Merged** - Labeling docs for contributing
- ✅ **PR #921 Merged** - CI test without API keys

## Last Updated
2025-12-03 19:10 UTC
