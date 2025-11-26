# Lessons System Deep Dive

**Date**: 2025-11-26 11:00 UTC
**Duration**: ~15 minutes
**Goal**: Understand how gptme's lessons system integrates with tool execution and LLM prompts

## System Architecture

### Discovery & Indexing (LessonIndex)

**Lesson Discovery Sources** (in priority order):
1. User config: `~/.config/gptme/lessons`
2. Current workspace: `./lessons`
3. Project-local: `./.gptme/lessons`
4. Cursor rules: `./.cursor` directory (.mdc files!)
5. Legacy: `.cursorrules` file (with migration guidance)
6. Configured: Directories from `gptme.toml`

**Indexing Process**:
- Discovers all `.md` and `.mdc` files recursively
- Skips special files (README.md, TODO.md, templates)
- Only includes lessons with `status: active`
- Logs warnings for parsing failures
- Caches parsed lessons in memory

### Lesson Format Support (parser.py)

**Three Format Types**:

1. **gptme Lesson Format**:
   ```yaml
   ---
   match:
     keywords: [patch, file, editing]
     tools: [patch]
   status: active
   ---
   ```

2. **Anthropic Skill Format**:
   ```yaml
   ---
   name: skill-name
   description: Brief description
   ---
   ```

3. **Cursor .mdc Format** (with automatic translation!):
   ```yaml
   ---
   name: Rule Name
   globs: ["**/*.py"]
   priority: high
   alwaysApply: true
   ---
   ```

**Glob-to-Keyword Translation**:
- Extracts file extensions: `*.py` → `["python", "python code"]`
- Maps 20+ extensions to relevant keywords
- Extracts directory context: `api/` → `["api"]`
- `alwaysApply: true` adds generic keywords: `["code", "development", "project"]`

### Matching System

**Two Matching Strategies**:

1. **Keyword-Only Matcher** (matcher.py):
   - Searches message for keywords (case-insensitive)
   - Keyword match = +1.0 score
   - Tool match = +2.0 score (higher weight)
   - Returns sorted by score descending

2. **Hybrid Matcher** (hybrid_matcher.py):
   - **Two-stage process**:
     - Stage 1: Fast keyword filtering (top 20 candidates)
     - Stage 2: Hybrid scoring on candidates

   - **Five scoring components**:
     - Keyword (25%): Normalized keyword relevance
     - Semantic (40%): Cosine similarity via sentence-transformers
     - Effectiveness (25%): Helpful/harmful counts (TODO - currently 0.5)
     - Recency (10%): Exponential decay (TODO - currently 1.0)
     - Tool bonus (20%): Additional bonus for tool matches

   - **Dynamic top-K selection**:
     - Minimum threshold: 0.6 (quality over quantity)
     - Maximum lessons: 10 (prevent context explosion)
     - No minimum safeguard (prevents cumulative degradation)

   - **Fallback**: Uses keyword-only if embeddings unavailable

### Auto-Inclusion Flow (auto_include.py)

**Triggered for each user message**:

1. Find last user message in conversation
2. Build MatchContext (message content, tools_used)
3. Get lesson index (discovers all lessons)
4. Choose matcher (hybrid or keyword-only)
5. Match lessons against context
6. Limit to top N matches (default 5, hybrid uses dynamic selection)
7. Format matches into system message
8. Insert after initial system message as hidden message

**Lesson Formatting**:
```markdown
# Relevant Lessons

## Lesson Title
*Path: lessons/category/lesson.md*
*Category: tools*
*Matched by: keyword:patch, keyword:file*

[Lesson body content...]
```

### Integration Points

**1. Context Selector** (selector_integration.py):
- `LessonItem` wraps lessons for context selector
- Provides: content, metadata, identifier
- Enables LLM-based lesson evaluation

**2. Commands** (commands.py):
- `/lesson list [category]` - List available lessons
- `/lesson search <query>` - Search by keyword/content
- `/lesson show <name>` - Display specific lesson
- `/lesson refresh` - Re-index all lessons

**3. Tool Execution**:
- Lessons included in context BEFORE tool execution
- Tool matches get higher score (2.0 vs 1.0)
- Helps LLM understand tool usage patterns

**4. LLM Prompts**:
- Lessons appear as hidden system messages
- Visible to LLM, hidden from UI by default
- Provide just-in-time guidance based on context

## Key Features

### Cursor Integration
- Native support for Cursor .mdc files
- Automatic glob-to-keyword translation
- Preserves Cursor metadata (priority, triggers, alwaysApply)
- Encourages migration from .cursorrules to .cursor/ directory

### Quality Controls
- Status filtering (only "active" lessons auto-included)
- Score thresholds prevent low-quality matches
- Dynamic top-K prevents context explosion
- Two-stage hybrid matching for efficiency

### Future Enhancements (Planned)
- ACE metadata schema: helpful_count, harmful_count, updated timestamps
- Effectiveness scoring based on user feedback
- Recency scoring with exponential decay
- Priority-based lesson selection
- Semantic search in lesson commands

## Implementation Notes

**Files Examined**:
- `gptme/lessons/index.py` - Lesson discovery and indexing
- `gptme/lessons/parser.py` - Format parsing and translation
- `gptme/lessons/matcher.py` - Basic keyword matching
- `gptme/lessons/hybrid_matcher.py` - Advanced hybrid scoring
- `gptme/lessons/auto_include.py` - Auto-inclusion flow
- `gptme/lessons/selector_integration.py` - Context selector integration
- `gptme/lessons/commands.py` - CLI commands

**Dependencies**:
- Optional: PyYAML (for frontmatter parsing)
- Optional: sentence-transformers (for semantic matching)
- Optional: numpy (for embedding calculations)

**Performance Characteristics**:
- Fast keyword filtering in Stage 1 (O(n) where n = lesson count)
- Semantic scoring only on top-K candidates
- Index cached in memory (refresh when needed)
- Per-turn lesson selection (not per-session accumulation)

## Lessons for Future Contributions

1. **Two-stage matching is key**: Fast filtering → expensive scoring
2. **Quality over quantity**: Strict thresholds prevent degradation
3. **Per-turn selection**: Prevents cumulative context pollution
4. **Fallback mechanisms**: Degrade gracefully when dependencies unavailable
5. **Format flexibility**: Support multiple lesson formats for broad adoption
6. **Automatic translation**: Convert Cursor globs to gptme keywords seamlessly

## Next Steps

Now equipped to:
- Create effective lessons with proper keywords
- Understand lesson selection logic for debugging
- Contribute to lessons system improvements
- Help users with lesson configuration issues
- Design new lesson matching strategies
