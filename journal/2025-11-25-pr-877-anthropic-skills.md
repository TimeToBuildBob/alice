# PR #877: Refactor Skills to Anthropic Format

**Date**: 2025-11-25 09:00-09:11 UTC
**Session**: Autonomous run (scheduled)
**Goal**: Address Erik's review feedback on PR #876 skills infrastructure

## Context

Erik provided comprehensive review feedback on PR #876, requesting:
1. Follow Anthropic's skill format (SKILL.md in folders)
2. Unify under lessons/ tree instead of separate skills/ tree
3. Use requirements.txt for dependencies
4. Remove non-Anthropic metadata fields

## Work Completed

### 1. Research Phase
- Used Perplexity to research Anthropic's skill format
- Key findings:
  - Skills are folders with SKILL.md file
  - YAML frontmatter: `name` and `description` only
  - Optional: scripts/, templates/, resources/ subdirectories
  - Dependencies in requirements.txt

### 2. Restructuring Phase
- Created new directory: `gptme/lessons/skills/python-repl/`
- Moved and renamed: `python-repl-skill.md` → `SKILL.md`
- Moved helper script: `python_helpers.py`
- Created `requirements.txt` with dependencies:
  ```
  ipython
  numpy
  pandas
  ```

### 3. Skill File Updates
- Updated YAML frontmatter to Anthropic format:
  ```yaml
  ---
  name: python-repl
  description: Interactive Python REPL automation with common helpers
  ---
  ```
- Removed deprecated fields: `type`, `match`, `scripts`, `dependencies`, `hooks`
- Updated documentation sections
- Fixed relative paths

### 4. Parser Updates
- Added `name` and `description` fields to `LessonMetadata`
- Removed skills-specific fields (type, scripts, dependencies, hooks)
- Updated parsing logic to extract Anthropic format fields
- Maintained backward compatibility with lesson format
- Updated docstring to document both formats

### 5. Documentation Updates
- Updated `docs/skills/README.md`:
  - Directory structure showing unified lessons/ tree
  - Skill creation instructions with Anthropic format
  - Test examples with new paths
  - Removed hooks documentation (Phase 4.2)

### 6. Discovery Verification
- Confirmed skills auto-discovered via existing `index.py`
- Uses `rglob("*.md")` to find all markdown files recursively
- No changes needed (resolves Greptile comment)

## Results

✅ **PR #877 Created**: https://github.com/gptme/gptme/pull/877
- Complete refactoring to Anthropic format
- All 6 inline review comments addressed
- Backward compatible with lesson format

✅ **Comment Posted on PR #876**: Linked to new PR #877

## Technical Details

### Files Changed
- `gptme/lessons/parser.py`: Added Anthropic format support
- `docs/skills/README.md`: Updated documentation
- `gptme/skills/examples/` → `gptme/lessons/skills/python-repl/`: Restructured
- New: `requirements.txt` for dependencies

### Commit
