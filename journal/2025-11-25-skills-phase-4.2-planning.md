# Skills Phase 4.2 - Hook System Planning

**Date**: 2025-11-25
**Context**: Planning next phase of issue #686 while PR #876 (Phase 4.1) is in review

## Phase 4.2 Goals

From issue #686, Phase 4.2 should implement:
- Hook system implementation
- Dependency management
- Script loading and execution
- Skills CLI commands

## Hook System Design

### Hook Types (from Phase 4.1 docs)

Based on `docs/skills/README.md` from PR #876, skills can define these hooks:

1. **pre_execute**: Run before skill's bundled scripts
2. **post_execute**: Run after skill's bundled scripts
3. **on_error**: Run when skill execution fails
4. **pre_context**: Run before skill is added to context
5. **post_context**: Run after skill is added to context

### Hook Implementation Architecture

**Option A: Event-Based System**
- Use a central event dispatcher
- Skills register hooks for specific events
- gptme emits events at key points (before tool execution, after generation, etc.)
- Pros: Flexible, extensible, decoupled
- Cons: More complex, potential performance overhead

**Option B: Explicit Hook Points**
- Add hook invocation at specific points in codebase
- Skills provide callback functions
- Direct function calls at hook points
- Pros: Simple, predictable, easier to debug
- Cons: Less flexible, tightly coupled

**Recommendation**: Start with Option B (explicit hook points) for Phase 4.2, then consider Event-Based for future phases if needed.

### Hook Execution Context

Each hook should receive context about current state:
- The skill being executed
- Current message (if applicable)
- Current conversation
- Available tools
- gptme configuration

### Hook Registration

Skills register hooks via metadata in YAML frontmatter, then implementation loads and validates them during skill initialization.

## Script Loading System

### Script Discovery

Skills bundle scripts in the same directory as the skill file:
skills/examples/
  python-repl-skill.md       # The skill definition
  python_helpers.py          # Bundled script

### Script Loading Mechanism

1. Parse skill metadata to get script list
2. Resolve script paths relative to skill file
3. Load scripts into memory or execution environment
4. Make available to tools via Python imports or tool functions

### Execution Model

**Option A: Dynamic Import**
```python
# Load skill scripts as Python modules
import importlib.util
spec = importlib.util.spec_from_file_location("skill_module", script_path)
module = importlib.util.module_from_spec(spec)
spec.loader.exec_module(module)
```

**Option B: Inject into IPython Context**
```python
# Add skill scripts to IPython namespace
with open(script_path) as f:
    exec(f.read(), ipython_namespace)
```

**Recommendation**: Option B (IPython injection) for Phase 4.2 since we already have IPython integration.

## Dependency Management

### Dependency Declaration

Skills declare dependencies in YAML:
```yaml
dependencies:
  - ipython>=8.0
  - numpy
  - pandas
```

### Dependency Checking

On skill load:
1. Check if dependencies are installed
2. Warn or fail if missing
3. Optionally suggest installation command

### Future Enhancement

Phase 4.3+ could add:
- Automatic dependency installation (opt-in)
- Virtual environment per skill
- Dependency version constraints

## Skills CLI Commands

### Proposed Commands

```bash
# List available skills
gptme skills list

# Show skill details
gptme skills show <skill-name>

# Validate skill format
gptme skills validate <skill-file>

# Enable/disable skills
gptme skills enable <skill-name>
gptme skills disable <skill-name>

# Install skill from URL or file
gptme skills install <source>
```

### Implementation Approach

Add new CLI subcommand in `gptme/cli.py`:
- Reuse existing lesson loading infrastructure
- Filter for `type: skill` in metadata
- Add skill-specific operations (script validation, dependency checking)

## Implementation Plan

### Step 1: Hook System Foundation
- [ ] Design HookContext dataclass
- [ ] Implement hook registration in skill loader
- [ ] Add hook invocation points in relevant code paths
- [ ] Add tests for hook execution

### Step 2: Script Loading
- [ ] Implement script discovery and path resolution
- [ ] Add script loading into IPython context
- [ ] Handle script errors gracefully
- [ ] Add tests for script loading

### Step 3: Dependency Management
- [ ] Add dependency checking on skill load
- [ ] Implement warning system for missing deps
- [ ] Document dependency best practices
- [ ] Add tests for dependency validation

### Step 4: Skills CLI
- [ ] Implement `skills list` command
- [ ] Implement `skills show` command
- [ ] Implement `skills validate` command
- [ ] Add integration tests

### Step 5: Documentation
- [ ] Update docs/skills/README.md with implementation details
- [ ] Add examples of hook usage
- [ ] Document CLI commands
- [ ] Create migration guide

## Open Questions

1. **Hook Priority**: If multiple skills have same hook, what order to execute?
2. **Hook Failure Handling**: Should one failing hook stop others?
3. **Script Namespace**: Should scripts share namespace or be isolated?
4. **Dependency Conflicts**: How to handle version conflicts between skills?
5. **Security**: Should we sandbox script execution?

## Next Steps

1. Wait for PR #876 (Phase 4.1) to merge
2. Create Phase 4.2 implementation issue
3. Implement hook system foundation
4. Add script loading
5. Build out CLI commands

## References

- Issue #686: https://github.com/gptme/gptme/issues/686
- PR #876: https://github.com/gptme/gptme/pull/876
- Claude Skills: https://simonwillison.net/2025/Oct/10/claude-skills/
