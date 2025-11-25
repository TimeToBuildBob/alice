# Work Queue

## Current Run
Session 20251125-1500: ✅ Completed Issue #686 Phase 1 (Cursor rules research), updated work queue with accurate state, posted findings to GitHub.

## Planned Next

1. **Issue #686 Phase 5: Cross-System Compatibility** (MEDIUM priority)
   - Priority: MEDIUM (autonomous-friendly implementation)
   - Goal: Enable gptme to read Cursor .mdc rules files
   - Status: READY - Phase 1 research complete, clear implementation path
   - Actions:
     - Extend lesson parser to support .mdc files
     - Create unified metadata schema
     - Build conversion tools (export/import)
     - Update documentation
   - Timeline: 3-4 hours
   - Source: Issue #686 Phase 5
   - Link: https://github.com/ErikBjare/gptme/issues/686
   - Prerequisites: ✅ Phase 1 research complete

2. **Apply Enhanced Labels to gptme Issues** (LOW priority)
   - Priority: LOW (maintenance work, judgment calls required)
   - Goal: Implement Issue #874 labeling system across open issues
   - Status: Labels created by Erik, need application
   - Action: Review open issues and apply difficulty/status/priority labels
   - Timeline: 2-3 hours
   - Source: Issue #874
   - Link: https://github.com/ErikBjare/gptme/issues/874
   - Note: Erik expects inconsistent application, set precedent carefully

3. **Complete Initial Agent Setup** (LOW priority, requires human)
   - Priority: LOW (not autonomous-friendly)
   - Goal: Establish Alice's identity, personality, goals, and values
   - Progress: ✅ Foundational ABOUT.md drafted (2025-11-23)
   - Next Action: Interactive session with creator to review and refine
   - Status: PARTIAL - Foundation established, needs creator confirmation
   - Timeline: 20-30 min interactive session
   - Source: tasks/initial-agent-setup.md
   - Note: Requires human interaction for identity refinement

4. **Issue #686 Phase 6: Documentation and Testing** (MEDIUM priority)
   - Priority: MEDIUM (some parts autonomous-friendly)
   - Goal: Document enhanced lesson system
   - Status: NOT STARTED
   - Potential Actions:
     - Draft user guide for lesson system
     - Create examples of metadata and triggers
     - Add tests for new functionality
   - Timeline: 3-4 hours
   - Source: Issue #686 Phase 6
   - Link: https://github.com/ErikBjare/gptme/issues/686

## Recently Completed

- ✅ **Issue #686 Phase 1 - Cursor Rules Research** (2025-11-25 15:19 UTC) - Completed comprehensive research on Cursor .mdc rules format using Perplexity, documented detailed comparison with gptme lessons, analyzed compatibility challenges, provided recommendations for Phase 5 implementation. Research document: knowledge/technical/cursor-rules-format-research.md. Posted findings to GitHub issue. (https://github.com/ErikBjare/gptme/issues/686#issuecomment-3576141034)
- ✅ **PR #879 Closed - Architectural Clarification** (2025-11-25 13:30 UTC) - PR closed (not merged) because hooks belong in plugins, not skills. Erik clarified: Skills = simple Anthropic folder format (SKILL.md + resources), Plugins = deep integration (hooks/tools/commands in Python packages). Example plugin with hooks created and merged in gptme-contrib#31. Key learning: Skills should remain lightweight knowledge bundles, hooks are plugin infrastructure. (https://github.com/ErikBjare/gptme/pull/879)
- ✅ **PR #877 Skills Refactoring MERGED** (2025-11-25 09:11 UTC) - Refactored skills to Anthropic format (https://github.com/ErikBjare/gptme/pull/877)
- ✅ **PR #873 Plugin Management MERGED** (2025-11-25 07:10 UTC) - Enhanced plugin management with smart src/ layout discovery (https://github.com/ErikBjare/gptme/pull/873)

## Last Updated
2025-11-25 15:20 UTC
