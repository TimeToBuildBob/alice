# Cursor Rules Format Research

**Date**: 2025-11-25
**Purpose**: Issue #686 Phase 1 - Understanding Cursor rules format for cross-system compatibility
**Researcher**: Alice (autonomous)

## Overview

Cursor IDE uses `.mdc` (Markdown with Context) rules files to instruct its AI assistant on code conventions, project standards, and specialized workflows. This research analyzes the format for potential integration with gptme's lesson system.

## Cursor Rules Format

### File Structure

```yaml
---
# Required metadata
name: Rule Name
description: Brief summary of rule's intent

# Optional metadata
globs: ["path/pattern/**/*.ext"]  # File path patterns
triggers: [file_change]            # When rule applies
priority: high                     # high/medium/low
version: "1.0"                     # Version tracking
alwaysApply: true                  # Boolean for always active
---

# Markdown content
Rule instructions, guidelines, examples...
```

### Directory Organization
.myproject/
└── .cursor/
    ├── index.mdc               # Project-wide standards (Always applies)
    └── rules/
        ├── rule-name-1.mdc     # Context-specific rule
        ├── rule-name-2.mdc     # Another context-specific rule
        └── ...

### Key Features

**1. Hierarchical Organization**
- Project-wide rules: `.cursor/index.mdc` with `alwaysApply: true`
- Context-specific rules: `.cursor/rules/*.mdc` for specialized guidance
- Each file focused on one concern

**2. Advanced Metadata**
- `globs`: File path patterns for auto-matching (e.g., `"src/**/*.py"`)
- `triggers`: When rule activates (file_change, etc.)
- `priority`: high/medium/low for rule precedence
- `version`: Track rule evolution
- `alwaysApply`: Boolean for global vs. conditional application

**3. Token Efficiency**
- Recommended ~100 lines per file maximum
- Bullet points preferred over paragraphs
- Modular, focused rules for efficient context loading

**4. Naming Conventions**
- Kebab-case filenames (e.g., `enforce-python-style.mdc`)
- Always use `.mdc` extension
- Descriptive, purpose-driven names

### Advanced Rule Structure Example

```yaml
---
name: Enforce Python Style
description: Enforces PEP8 compliance for all Python files.
globs:
  - "src/**/*.py"
triggers:
  - file_change
priority: high
version: "1.0"
---
rule_definition:
  description: "This rule ensures Python source files in src/ follow PEP8."
  checks:
    - flake8
    - black
  actions:
    - type: review
      on_fail: alert_developers
```

## gptme Lessons Format (Current)

### File Structure

```yaml
---
# Metadata
keywords: [keyword1, keyword2]  # Trigger words for auto-inclusion
tags: [category]                 # Categorization
created: YYYY-MM-DD             # Creation date
updated: YYYY-MM-DD             # Last update
---

# Markdown content
## Rule
Concise imperative statement

## Context
When this applies

## Detection
Observable signals

## Pattern
Minimal correct example

## Outcome
Expected result

## Related
Links to related resources
```

### Directory Organization
workspace/
└── lessons/
    ├── tools/
    │   ├── lesson-name.md
    │   └── ...
    ├── patterns/
    │   └── ...
    └── workflow/
        └── ...

# Or in project-specific context
.myproject/
└── .gptme/
    └── lessons/
        └── *.md

### Key Features

**1. Two-File Architecture**
- Primary lesson: ~30-50 lines (LLM-optimized for context inclusion)
- Companion doc: Unlimited (implementation details, examples)
- Token-efficient approach

**2. Keyword-Based Activation**
- `keywords`: List of trigger words/phrases
- Auto-included when keywords match conversation context
- Multiple keywords can trigger same lesson

**3. Structured Format**
- Rule: Imperative statement of what to do
- Context: When this applies
- Detection: How to recognize need
- Pattern: Minimal correct example
- Outcome: Expected result
- Related: Cross-references

**4. Categorization**
- Organized by category (tools/, patterns/, workflow/, social/)
- Tags for additional classification
- Timestamps for tracking evolution

## Comparison: Cursor vs gptme

| Aspect | Cursor Rules (.mdc) | gptme Lessons (.md) |
|--------|-------------------|-------------------|
| **File Extension** | `.mdc` | `.md` |
| **Activation** | File path patterns (`globs`) | Context keywords |
| **Scope Control** | `alwaysApply` boolean | Keywords presence |
| **Metadata Fields** | name, description, globs, triggers, priority, version | keywords, tags, created, updated |
| **Organization** | Hierarchical (.cursor/index.mdc + rules/) | Categorized (tools/, patterns/, etc.) |
| **Size Guideline** | ~100 lines max | ~30-50 lines primary (+ companion) |
| **Priority System** | high/medium/low explicit | Implicit via keyword matching order |
| **Versioning** | Explicit version field | Timestamps (created/updated) |
| **Structure** | Free-form Markdown | Structured sections (Rule/Context/Pattern) |
| **Use Case** | IDE-specific code conventions | General AI agent behavioral guidance |

## Compatibility Analysis

### Strengths for Cross-Compatibility

1. **Shared Foundation**: Both use YAML frontmatter + Markdown body
2. **Similar Purpose**: Both provide AI guidance on behavior/conventions
3. **Modular Design**: Both support multiple files for different concerns
4. **Token Awareness**: Both emphasize concise, efficient content

### Key Differences

1. **Activation Mechanism**
   - Cursor: File-path based (`globs`) - "Apply when editing these files"
   - gptme: Context-based (`keywords`) - "Apply when discussing these topics"

2. **Scope Philosophy**
   - Cursor: Project-scoped, file-type specific
   - gptme: Conversation-scoped, topic/tool specific

3. **Metadata Richness**
   - Cursor: More structured (priority, version, triggers, actions)
   - gptme: Simpler (keywords, tags, timestamps)

4. **Content Structure**
   - Cursor: Free-form Markdown
   - gptme: Structured sections (Rule/Context/Detection/Pattern/Outcome)

### Translation Challenges

1. **globs → keywords**: File patterns don't directly map to context keywords
   - Example: `"**/*.py"` could map to `["python", "python code"]`
   - But reverse is ambiguous

2. **alwaysApply → keywords**: Boolean vs. presence-based
   - `alwaysApply: true` could map to high-frequency keywords
   - But doesn't capture "always" semantics

3. **priority → keyword order**: Explicit vs. implicit
   - Cursor's priority is explicit
   - gptme's priority is implicit (keyword match frequency/order)

4. **triggers → keywords**: Action-based vs. topic-based
   - Cursor: `file_change`, `build_error`, etc.
   - gptme: Topic/tool/pattern names

## Recommendations for Cross-System Compatibility

### Phase 1: Support Both Formats (Read-Only)

**Goal**: Allow gptme to read and use both `.md` (gptme) and `.mdc` (Cursor) format files

**Implementation**:
```python
# Extend lesson parser to support .mdc files
def parse_lesson(path: Path) -> Lesson:
    if path.suffix == ".mdc":
        return parse_cursor_rule(path)
    elif path.suffix == ".md":
        return parse_gptme_lesson(path)
```

**Metadata Translation**:
- Cursor `name` → gptme `title`
- Cursor `description` → gptme `description`
- Cursor `globs` → infer `keywords` (e.g., `"**/*.py"` → `["python"]`)
- Cursor `priority` → adjust keyword weight
- Cursor `alwaysApply: true` → add to high-frequency keywords

### Phase 2: Unified Metadata Schema (Optional)

**Goal**: Create superset schema that supports both systems

**Extended Frontmatter**:
```yaml
---
# Universal fields
name: Rule Name                    # Cursor format
keywords: [keyword1, keyword2]     # gptme format
description: Brief summary

# Cursor-specific
globs: ["**/*.ext"]                # Optional
alwaysApply: false                 # Optional
triggers: [file_change]            # Optional
priority: high                     # Optional

# gptme-specific
tags: [category]                   # Optional
created: YYYY-MM-DD                # Optional
updated: YYYY-MM-DD                # Optional
---
```

**Benefits**:
- Single file format works in both systems
- Gradual migration path
- Preserves system-specific features

### Phase 3: Export/Import Tools

**Goal**: Convert between formats with best-effort translation

**gptme → Cursor**:
```bash
gptme export-cursor-rule lesson.md > rule.mdc
```
- Map `keywords` to reasonable `globs` if possible
- Add `alwaysApply: true` for high-frequency keywords
- Preserve content structure

**Cursor → gptme**:
```bash
gptme import-cursor-rule rule.mdc > lesson.md
```
- Infer `keywords` from `globs` and content
- Convert to gptme structure (Rule/Context/Pattern)
- Add timestamps

### Phase 4: Documentation

**Goal**: Guide users on cross-system usage

**Topics**:
1. How to write lessons that work in both systems
2. Metadata translation reference
3. Best practices for portable lessons
4. Known limitations and workarounds

## Recommendations for Issue #686

### Phase 1 (Current Research): ✅ COMPLETE
- [x] Research Cursor rules format
- [x] Document structure and metadata
- [x] Analyze compatibility with gptme
- [x] Identify translation challenges

### Phase 5 (Cross-System Compatibility):

**Task 1**: Extend lesson parser to support `.mdc` files
- Read both `.md` and `.mdc` formats
- Translate Cursor metadata to gptme format
- Map `globs` to `keywords` with heuristics

**Task 2**: Create unified metadata schema
- Design superset frontmatter that supports both
- Update gptme lesson parser to accept optional Cursor fields
- Document compatibility matrix

**Task 3**: Build conversion tools
- `gptme export-cursor-rule` command
- `gptme import-cursor-rule` command
- Best-effort translation with warnings for limitations

**Task 4**: Update documentation
- Add Cursor compatibility guide to gptme docs
- Show examples of cross-compatible lessons
- Document metadata translation rules

## Use Cases for Cross-Compatibility

1. **Existing Cursor Users**: Can import their `.mdc` rules into gptme agent workflows
2. **Multi-Tool Projects**: Use same lesson files for both Cursor IDE and gptme agents
3. **Knowledge Sharing**: Community can share rules/lessons across tools
4. **Gradual Migration**: Move between systems without rewriting all rules/lessons

## Open Questions

1. **Priority Mapping**: How to map Cursor's explicit priority to gptme's implicit keyword-based priority?
   - Suggestion: Add priority field to gptme metadata

2. **Always-Apply Semantics**: How to handle `alwaysApply: true` in gptme?
   - Suggestion: Add to system message or high-priority auto-include list

3. **Glob Inference**: What heuristics for converting file globs to keywords?
   - `"**/*.py"` → `["python", "python code"]`
   - `"src/api/**/*.ts"` → `["typescript", "api", "backend"]`
   - Need more examples and refinement

4. **Trigger Translation**: How to map Cursor's action triggers to gptme's context keywords?
   - May not be directly translatable
   - Could be metadata that gptme ignores

## References

- Cursor IDE documentation: [cursor.sh](https://cursor.sh)
- Cursor rules examples: Community repositories
- gptme lessons: `gptme/lessons/` and agent workspaces
- Issue #686: Enhanced lesson system roadmap

## Next Steps

1. **Discuss findings** with maintainers (Erik)
2. **Prototype parser** for `.mdc` support
3. **Create example** cross-compatible lesson
4. **Document** best practices

---

**Research Complete**: 2025-11-25 15:15 UTC
**Time Spent**: 15 minutes
**Token Usage**: ~40k total
**Status**: Ready for review and Phase 5 planning
