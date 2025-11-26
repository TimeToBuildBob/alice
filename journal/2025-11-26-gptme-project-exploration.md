# gptme Project Exploration

**Date**: 2025-11-26 09:00 UTC
**Session**: Autonomous 20251126-0900
**Duration**: ~20 minutes
**Status**: ✅ COMPLETED

## Objective

Explore gptme project structure, architecture, code quality standards, and contribution patterns to build foundational knowledge for future autonomous contributions.

## Key Findings

### 1. Architecture (Five Extensibility Mechanisms)

Learned from `docs/concepts.rst` (new file, 451 lines added in recent commit):

1. **Knowledge Files**: Lightweight context bundles (Anthropic skill format)
2. **Tools**: Capabilities the AI can execute (ToolSpec-based)
3. **Hooks**: Lifecycle integration points
4. **Commands**: User interface shortcuts
5. **Plugins**: Packaging mechanism for tools/hooks/commands

Clear separation of concerns with well-defined interfaces.

### 2. ToolSpec Architecture

Examined `gptme/tools/base.py` (25KB):

**Core Design**:
- Dataclass-based with frozen=True for immutability
- Strong typing with Protocol, TypeAlias, Literal
- Parameter system with JSON schema generation
- Multi-format support (markdown, xml, tool)

**Key Components**:
```python
@dataclass(frozen=True)
class ToolSpec:
    name: str
    desc: str
    instructions: str
    examples: str | Callable
    functions: list[Callable] | None
    execute: ExecuteFunc | None
    block_types: list[str]
    hooks: dict[str, tuple[str, HookFunc, int]]
    commands: dict[str, Callable]
```

**Integration Points**:
- `register_hooks()`: Lifecycle integration
- `register_commands()`: User shortcuts
- `get_doc()`: Auto-generated documentation
- MCP support via `is_mcp` flag

### 3. Code Quality Standards

Examined `gptme/lessons/parser.py` and `tests/test_lessons_parser.py`:

**Patterns Observed**:
- **Type Hints**: Python 3.10+ union syntax (`str | None`)
- **Docstrings**: Comprehensive with examples
- **Error Handling**: Graceful degradation (HAS_YAML check)
- **Testing**: Class-based organization, descriptive names, edge cases
- **Modern Python**: dataclasses, Protocol, TypeAlias

**Example Test Pattern**:
```python
class TestExtractTitle:
    def test_extract_title_with_h1(self):
        """Test extracting title from H1 heading."""
        content = "# My Title\n\nContent"
        assert _extract_title(content) == "My Title"
```

### 4. Recent Development Focus

From `git log` and recent changes:

**Active Areas**:
- Lessons system enhancements (195 lines in parser.py)
- LLM provider integrations (159 lines in llm_anthropic.py)
- Constrained decoding support (PR #776 merged successfully)
- Documentation improvements (concepts.rst)

**Recent Commits Show**:
- Strong focus on extensibility
- Testing infrastructure improvements
- Documentation-first approach

### 5. Issue Labeling System (Ready for Application)

Examined Issue #874 and verified label creation:

**All Labels Created** (4 dimensions, ~15 labels):

**Difficulty**: easy, medium, hard
**Status**: ready, needs-design, blocked, in-progress, has-pr
**Work-Type**: autonomous-friendly, needs-human-judgement
**Priority**: critical, high, medium, low

**Status**: Labels created by Erik, ready for application to open issues.

**Note from Work Queue**: Erik expects inconsistent application, wants precedent set carefully. Task marked LOW priority due to judgment calls required.

### 6. Project Statistics

From README.md:

**Badges/Stats**:
- Active CI/CD (GitHub Actions)
- High test coverage (Codecov integration)
- PyPI package with daily downloads tracked
- Discord community active
- Strong documentation site

**Tools Available**: 30+ tools in `gptme/tools/` including:
- Core: shell, python, patch, save, read
- Advanced: browser, computer, subagent, gh
- Specialized: tmux, tts, vision, mcp

## Knowledge Gained

### Understanding Contribution Patterns

1. **Code Changes**:
   - Use type hints consistently
   - Write comprehensive tests
   - Document with examples
   - Follow modern Python patterns

2. **Tool Development**:
   - Use ToolSpec dataclass
   - Register hooks/commands when needed
   - Provide clear instructions and examples
   - Support multiple formats (markdown/xml/tool)

3. **Documentation**:
   - RST for Sphinx docs
   - Markdown for user content
   - Include code examples
   - Use Mermaid for architecture diagrams

### Future Work Enabled

This exploration enables:

1. **Label Application** (Task #1):
   - Understand labeling system
   - Know all available labels
   - Can make informed judgments about difficulty/status

2. **Tool Contributions**:
   - Understand ToolSpec architecture
   - Know how to integrate hooks/commands
   - Can extend existing tools

3. **Lessons System Work**:
   - Understand parser architecture
   - Know testing patterns
   - Can contribute improvements

4. **Code Reviews**:
   - Understand quality standards
   - Know testing expectations
   - Can provide informed feedback

## Files Examined

- `/home/bob/alice/projects/gptme/README.md` - Project overview
- `/home/bob/alice/projects/gptme/docs/concepts.rst` - Architecture (new)
- `/home/bob/alice/projects/gptme/gptme/tools/base.py` - ToolSpec design
- `/home/bob/alice/projects/gptme/gptme/lessons/parser.py` - Code quality example
- `/home/bob/alice/projects/gptme/tests/test_lessons_parser.py` - Test patterns
- Issue #874 (via gh CLI) - Labeling system proposal

## Next Steps

**Immediate**:
- Update work queue with exploration completion
- Consider label application task (LOW priority)
- Look for other autonomous-friendly tasks

**Future Sessions**:
- Deep dive into specific tool implementations
- Study lessons system integration patterns
- Explore MCP adapter architecture

## Impact

This 20-minute exploration significantly enhances autonomous contribution capability:

- **Knowledge Base**: Solid understanding of project architecture
- **Code Quality**: Clear standards for contributions
- **Tool System**: Ready to extend or modify tools
- **Labeling**: Can apply enhanced labels with context

---

**Duration**: 20 minutes focused exploration
**Token Efficiency**: Used head/sed for selective reading
**Outcome**: Comprehensive understanding of gptme architecture and contribution patterns
