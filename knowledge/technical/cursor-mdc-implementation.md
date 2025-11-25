# Cursor .mdc Rules Implementation

**Date**: 2025-11-25
**Issue**: #686 Phase 5 - Cross-System Compatibility
**Status**: Implemented

## Overview

Implemented support for reading Cursor IDE `.mdc` (Markdown with Context) rules files directly in gptme. This enables cross-system compatibility, allowing users to:

1. Use existing Cursor rules in gptme without conversion
2. Share rules/lessons across both tools
3. Maintain a single set of rules for IDE and agent workflows

## Implementation Details

### 1. Extended Parser (`gptme/lessons/parser.py`)

**Added Fields to LessonMetadata**:
```python
globs: list[str]           # File path patterns
priority: str | None       # high/medium/low
triggers: list[str]        # Action triggers
always_apply: bool         # Always-active flag
version: str | None        # Version tracking
```

**Glob-to-Keyword Translation**:
- Implemented `_glob_to_keywords()` function
- Maps file extensions to language keywords
- Extracts context from directory names
- Examples:
  - `**/*.py` → `['python', 'python code']`
  - `src/api/**/*.js` → `['javascript', 'api', 'backend']`
  - `**/*.tsx` → `['typescript', 'react', 'frontend']`

**Cursor Metadata Translation**:
- Implemented `_translate_cursor_metadata()` function
- Converts Cursor frontmatter to gptme format
- Maps `globs` to `keywords` using heuristics
- Handles `alwaysApply` by adding high-frequency keywords
- Preserves Cursor-specific fields for future use

**Format Detection**:
- Detects `.mdc` file extension
- Also detects `.md` files with `globs` field as Cursor format
- Maintains backward compatibility with standard gptme lessons

### 2. Extended Index (`gptme/lessons/index.py`)

**Directory Discovery**:
- Added `.cursor/` directory to default search paths
- Searches for both `*.md` and `*.mdc` files
- Provides helpful logging when Cursor directories found

**File Discovery**:
- Modified `_index_directory()` to find both extensions
- Updated to handle `.mdc` files seamlessly

### 3. Test Coverage (`tests/test_lessons_parser.py`)

**Added Test Classes**:
- `TestGlobToKeywords`: Tests glob-to-keyword translation
- `TestTranslateCursorMetadata`: Tests metadata translation
- `TestParseMdcLesson`: Tests complete .mdc parsing

**Test Scenarios**:
- Basic .mdc parsing
- Multiple glob patterns
- Priority and version handling
- `alwaysApply` flag behavior
- Triggers field preservation
- Backward compatibility with .md lessons

### 4. Example Files

Created sample `.mdc` files to demonstrate:
- `.cursor/index.mdc`: Project-wide standards with `alwaysApply: true`
- `.cursor/rules/python-style.mdc`: Language-specific rules with globs

## File Format Comparison

### Cursor .mdc Format
```yaml
---
name: Rule Name
description: Brief summary
globs: ["**/*.py"]
priority: high
alwaysApply: true
triggers: [file_change]
version: "1.0"
---
# Rule content...
```

### gptme Lesson Format
```yaml
---
match:
  keywords: [python, code]
status: active
---
# Lesson content...
```

### Unified Format (Supported)
```yaml
---
# Cursor fields
name: Rule Name
description: Brief summary
globs: ["**/*.py"]
priority: high

# gptme fields
match:
  keywords: [python, code]
status: active
---
# Content works in both systems
```

## Usage

### For Cursor Users

Simply place your `.mdc` rules in `.cursor/` directory:

```bash
project/
├── .cursor/
│   ├── index.mdc          # Always-applied project rules
│   └── rules/
│       ├── python.mdc     # Python-specific rules
│       └── typescript.mdc # TypeScript-specific rules
```

gptme will automatically discover and use these rules, translating `globs` to `keywords` for context matching.

### For gptme Users

Continue using standard `.md` lessons, or adopt Cursor format for cross-tool compatibility:

```bash
project/
├── .gptme/
│   └── lessons/
│       └── my-lesson.md   # Standard gptme format
└── .cursor/
    └── rules/
        └── shared.mdc     # Cursor format, works in gptme
```

### Writing Cross-Compatible Rules

For maximum compatibility, include both formats:

```yaml
---
# Cursor format (for IDE)
name: Python Best Practices
description: Python coding standards
globs: ["**/*.py"]
priority: high

# gptme format (for agent)
match:
  keywords: [python, python code, coding standards]
status: active
---

# Content that works in both systems
Use clear variable names and type hints...
```

## Translation Logic

### Glob Patterns to Keywords

The `_glob_to_keywords()` function uses:

1. **Extension Mapping**: Predefined map of file extensions to language keywords
2. **Directory Context**: Extracts meaningful directory names (api, tests, etc.)
3. **Deduplication**: Removes duplicate keywords while preserving order

### Metadata Mapping

| Cursor Field | gptme Equivalent | Notes |
|-------------|------------------|-------|
| `name` | `name` | Direct mapping |
| `description` | `description` | Direct mapping |
| `globs` | `keywords` | Translated via heuristics |
| `priority` | `priority` | Preserved for future use |
| `alwaysApply` | `keywords` | Adds high-frequency keywords |
| `triggers` | `triggers` | Preserved, not used yet |
| `version` | `version` | Preserved for tracking |

## Limitations

1. **Glob Translation**: File patterns don't directly map to context keywords
   - Loss of file-path specificity
   - May match more broadly than intended
   - Best-effort translation with room for improvement

2. **alwaysApply Semantics**: Boolean flag doesn't fully translate to keyword presence
   - Currently adds generic keywords
   - May not match as frequently as Cursor's "always" behavior

3. **Triggers**: Action-based triggers (`file_change`) don't map to context keywords
   - Preserved in metadata but not used for matching
   - Could be implemented in future with hooks

4. **Priority**: Explicit priority doesn't map to implicit keyword-based priority
   - Preserved but not enforced in current matching

## Future Enhancements

### Phase 6: Export/Import Tools (Planned)

```bash
# Convert gptme lesson to Cursor rule
gptme lesson export-cursor lesson.md > rule.mdc

# Convert Cursor rule to gptme lesson
gptme lesson import-cursor rule.mdc > lesson.md
```

### Advanced Features (Potential)

1. **Priority System**: Use Cursor's explicit priority in lesson matching
2. **Trigger Hooks**: Map Cursor triggers to gptme hook system
3. **Semantic Matching**: Use LLM to improve glob-to-keyword translation
4. **Bidirectional Sync**: Keep .md and .mdc files in sync

## Testing

Run tests with:
```bash
poetry run pytest tests/test_lessons_parser.py::TestParseMdcLesson -v
```

Key test scenarios:
- Basic .mdc parsing with globs
- Multiple glob patterns
- Priority and version fields
- alwaysApply behavior
- Backward compatibility with .md

## Related Documentation

- Phase 1 Research: `knowledge/technical/cursor-rules-format-research.md`
- Lesson System: `lessons/README.md`
- Issue #686: Enhanced Lesson System Roadmap

## Conclusion

This implementation provides foundational support for Cursor `.mdc` rules in gptme, enabling:
- ✅ Read Cursor rules without conversion
- ✅ Automatic discovery of `.cursor/` directories
- ✅ Glob-to-keyword translation
- ✅ Metadata preservation
- ✅ Backward compatibility with gptme lessons

The feature enables cross-system compatibility while maintaining the unique strengths of each tool's approach to AI guidance.

---

**Implemented by**: Alice (autonomous)
**Date**: 2025-11-25
**Status**: Complete - Ready for testing and review
