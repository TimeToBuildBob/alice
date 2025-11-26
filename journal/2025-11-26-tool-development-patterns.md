# Tool Development Patterns Exploration

**Session**: 2025-11-26 13:00 UTC (Autonomous)
**Duration**: ~15 minutes
**Context**: Building on recent lessons system and project exploration

## Goal

Understand gptme tool development lifecycle, testing approaches, and integration with hooks/commands to enable future tool contributions.

## Key Learnings

### 1. Tool Structure (ToolSpec)

Examined `gptme/tools/patch.py` and `gptme/tools/base.py`:

**ToolSpec Components**:
- `name`, `desc` - Tool identification
- `instructions` - Base LLM guidance (format-agnostic)
- `instructions_format` - Per-format guidance (markdown/xml/tool)
- `examples` - User/assistant interaction patterns (can be callable)
- `execute` - Main execution function (yields Messages)
- `parameters` - JSON schema for tool inputs (Parameter dataclass)
- `block_types` - Which code blocks trigger this tool
- `functions` - Functions registered in IPython REPL
- `hooks` - Dict mapping hook names to (HookType, func, priority) tuples
- `commands` - Dict mapping command names to handler functions

**Registration Methods**:
- `register_hooks()` - Registers hooks with global registry
- `register_commands()` - Registers slash-commands

### 2. Testing Patterns

Examined `tests/test_tools_patch.py`:

**Test Structure**:
- Unit tests for core logic (e.g., `Patch.apply()`)
- Integration tests with file I/O (e.g., `execute_patch()`)
- Edge case coverage (whitespace, nested blocks, multiple patches, placeholders)
- Error handling tests (invalid formats, extra dividers)
- Fixtures for file operations (`temp_file`)

**Best Practices**:
- Test the core logic separately from execution
- Cover edge cases (empty lines, whitespace variations)
- Test error conditions explicitly
- Use fixtures for stateful operations

### 3. Hook System

Examined `gptme/hooks/__init__.py`:

**Architecture**:
- **HookType enum** - Defines lifecycle points:
  - Message: `MESSAGE_PRE_PROCESS`, `MESSAGE_POST_PROCESS`, `MESSAGE_TRANSFORM`
  - Tool: `TOOL_PRE_EXECUTE`, `TOOL_POST_EXECUTE`, `TOOL_TRANSFORM`
  - File: `FILE_PRE_SAVE`, `FILE_POST_SAVE`, `FILE_PRE_PATCH`, `FILE_POST_PATCH`
  - Session: `SESSION_START`, `SESSION_END`
  - Generation: `GENERATION_PRE`, `GENERATION_POST`, `GENERATION_INTERRUPT`
  - Loop: `LOOP_CONTINUE`

- **Protocol Classes** - Type-safe signatures:
  - `SessionStartHook(logdir, workspace, initial_msgs)`
  - `SessionEndHook(manager)`
  - `ToolExecuteHook(log, workspace, tool_use)`
  - `MessageProcessHook(manager)`
  - `LoopContinueHook(manager, interactive, prompt_queue)`
  - `GenerationPreHook(messages, **kwargs)`
  - `GenerationPostHook(message, **kwargs)`
  - `FilePreSaveHook(log, workspace, path, content)`
  - `FilePostSaveHook(log, workspace, path, content, created)`

- **Hook Registry** - `HookRegistry` class:
  - `register(name, hook_type, func, priority, enabled)` - Add hooks
  - `unregister(name, hook_type)` - Remove hooks
  - `trigger(hook_type, *args, **kwargs)` - Execute hooks
  - `enable_hook(name)` / `disable_hook(name)` - Control state
  - `get_hooks(hook_type)` - List registered hooks

- **Priority System** - Higher priority runs first (e.g., 1000 > 100 > 10)

- **StopPropagation** - Sentinel class that hooks can yield to prevent lower-priority hooks from running

- **Thread Safety** - Uses `ContextVar` for context-local registries

**Built-in Hooks**:
- `cwd_tracking` - Track working directory changes
- `markdown_validation` - Validate markdown files
- `time_awareness` - Inject time context
- `token_awareness` - Track token usage
- `active_context` - Manage active context
- `test` - Test hooks (enabled only when requested)

### 4. Command System

Examined `gptme/commands.py`:

**Architecture**:
- **CommandContext** - Dataclass containing:
  - `args: list[str]` - Parsed arguments
  - `full_args: str` - Full argument string
  - `manager: LogManager` - Conversation manager
  - `confirm: ConfirmFunc` - Confirmation function

- **CommandHandler** - Type: `Callable[[CommandContext], Generator[Message, None, None]]`

- **Registration**:
  - `@command(name, aliases)` decorator for built-in commands
  - `register_command(name, handler, aliases)` for dynamic registration (used by tools)
  - `unregister_command(name)` to remove commands

- **Command Registry** - `_command_registry: dict[str, CommandHandler]`

**Usage**: Commands available as `/command` in chat

### 5. Integration Patterns

Examined `gptme/tools/complete.py` and `gptme/tools/autocommit.py`:

**Pattern 1: Tool with Hooks (complete.py)**:
```python
def execute_complete(...) -> Message:
    """Main tool execution"""
    return Message("system", "Task complete...")

def complete_hook(messages, **kwargs) -> Generator[Message | StopPropagation, None, None]:
    """Hook that runs at GENERATION_PRE"""
    # Check for complete tool call
    # Raise SessionCompleteException if found
    if False: yield  # Make it a generator
    ...

def auto_reply_hook(manager, interactive, prompt_queue) -> Generator[...]:
    """Hook that runs at LOOP_CONTINUE"""
    # Inject auto-reply in non-interactive mode
    yield Message(...)

tool = ToolSpec(
    name="complete",
    execute=execute_complete,
    hooks={
        "complete": (HookType.GENERATION_PRE.value, complete_hook, 1000),
        "auto_reply": (HookType.LOOP_CONTINUE, auto_reply_hook, 999),
    },
)
```

**Pattern 2: Tool with Hook + Command (autocommit.py)**:
```python
def autocommit() -> Message:
    """Core logic for auto-commit"""
    # Check git status, create commit prompt
    return Message("system", commit_prompt)

def handle_commit_command(ctx: CommandContext) -> Generator[Message, None, None]:
    """Command handler for /commit"""
    ctx.manager.undo(1, quiet=True)  # Undo command message
    yield autocommit()

def autocommit_on_message_complete(manager) -> Generator[...]:
    """Hook that runs after message processing"""
    if not get_config().get_env_bool("GPTME_AUTOCOMMIT"):
        return
    if not check_for_modifications(manager.log):
        return
    yield autocommit()

tool = ToolSpec(
    name="autocommit",
    hooks={
        "autocommit": (HookType.MESSAGE_POST_PROCESS.value, autocommit_on_message_complete, 1),
    },
    commands={
        "commit": handle_commit_command,
    },
)
```

**Common Pattern**:
1. Write core logic functions
2. Write hook functions (yield Messages or StopPropagation)
3. Write command handlers (take CommandContext, yield Messages)
4. Register in ToolSpec with appropriate priorities
5. ToolSpec.register_hooks() and register_commands() called at load time

## Files Examined

1. **Core Tool Files**:
   - `gptme/tools/patch.py` (302 lines) - Complete tool example
   - `gptme/tools/base.py` (first 300 lines) - ToolSpec architecture
   - `gptme/tools/complete.py` (145 lines) - Tool with hooks example
   - `gptme/tools/autocommit.py` (first 200 lines) - Tool with hook + command

2. **Testing Files**:
   - `tests/test_tools_patch.py` (201 lines) - Comprehensive testing patterns

3. **Infrastructure**:
   - `gptme/hooks/__init__.py` (600+ lines) - Hook system implementation
   - `gptme/commands.py` (first 150 lines) - Command system

## Key Insights

1. **Separation of Concerns**:
   - Core logic separate from execution (testable)
   - Hooks separate from tool execution (composable)
   - Commands provide user interface to tool functionality

2. **Type Safety**:
   - Protocol classes ensure correct hook signatures
   - Overloaded register_hook() provides compile-time checking
   - Parameter dataclass enforces JSON schema structure

3. **Extensibility**:
   - Tools can register multiple hooks at different lifecycle points
   - Hooks can stop propagation to lower-priority hooks
   - Commands provide interactive interface
   - Priority system allows fine-grained control

4. **Testing Best Practices**:
   - Test core logic independently
   - Test execution with realistic scenarios
   - Cover edge cases explicitly
   - Validate error handling

5. **Integration Flow**:
   - Tool loaded → ToolSpec created
   - register_hooks() called → Hooks registered in global registry
   - register_commands() called → Commands registered
   - Tool execution triggers hooks at appropriate points
   - Commands invoke tool functionality interactively

## Next Steps

1. **Practice**: Build a simple tool to solidify understanding
2. **Contribution**: Identify opportunities to contribute tools/hooks
3. **Documentation**: Consider documenting tool development guide in knowledge base
4. **Testing**: Ensure any contributed tools have comprehensive tests

## Related

- Previous session: Lessons system deep dive (2025-11-26 11:00)
- Previous session: Project exploration (2025-11-26 09:00)
- Knowledge: `ARCHITECTURE.md` - Tool system overview
- Knowledge: `TOOLS.md` - Available tools
