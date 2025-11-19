# Context Generation System

This document describes the context generation system that provides automatic workspace context to gptme sessions.

## Overview

The context generation system is a modular shell script framework that automatically generates rich context summaries about the workspace state. It runs at the start of every gptme session via the `context_cmd` setting in `gptme.toml`.

**Purpose**: Provide comprehensive workspace awareness without manual context gathering.

**Architecture**: Main orchestrator script that calls specialized component scripts.

**Integration**: Configured in `gptme.toml`:
```toml
context_cmd = "scripts/context.sh"
```

## System Components

### Main Orchestrator: scripts/context.sh

**Purpose**: Coordinates all context generation components and produces unified output.

**Responsibilities**:
- Makes all component scripts executable
- Runs each component in sequence
- Adds section headers and dividers
- Includes git status at the end
- Enforces UTF-8 encoding

**Output Structure**:
```text
# Context Summary
Generated on: [timestamp]
---
[Journal Context]
# Tasks
[Task status from tasks.py or note if not installed]
[Workspace Structure]
# Git
[Git status output]
```

**Key Features**:
- Ensures all scripts are executable before running
- UTF-8 encoding enforcement for consistent output
- Clean section separation with headers
- Sequential execution of components

**Example Usage**:
```bash
# Run manually to see what context will be generated
./scripts/context.sh

# Automatically run by gptme via gptme.toml
gptme
```

### Component: scripts/context-journal.sh

**Purpose**: Extract and format recent journal entries for context.

**Features**:
- Finds most recent journal date across all files
- Groups entries by date
- Shows multiple sessions from same day
- Distinguishes today vs. past journals
- Full content for recent entries (configurable: MAX_FULL_ENTRIES=10)
- Path-only listing for older entries

**Date Detection**:
- Extracts date from filename pattern: `YYYY-MM-DD*.md`
- Sorts by modification time within same date
- Identifies today, yesterday, or past dates

**Output Format Example**:
```text
# Journal Context

Today's Journal Entry (2 sessions):

## Session: task-setup
[Full journal content]

## Session: knowledge-expansion
[Full journal content]

## Older Sessions (read with cat if relevant)
- journal/2025-11-17-first-run.md
```

**Smart Warnings**:
If journal is not from today, shows prominent warning with suggested filename for new journal entry.

**Configuration**:
- `MAX_FULL_ENTRIES`: Number of most recent entries to include in full (default: 10)

### Component: scripts/context-workspace.sh

**Purpose**: Generate workspace directory structure overview.

**Features**:
- Uses `tree` command for directory visualization
- ASCII-only output for compatibility
- Different depth levels for different directories
- Focused on key workspace areas

**Tree Configuration**:
- `-a`: Show all files (including hidden)
- `--dirsfirst`: List directories before files
- `--noreport`: Omit file/directory count summary
- `-L N`: Limit depth to N levels
- `LANG=C`: Force ASCII output (no Unicode characters)

**Directory Coverage**:
1. **Root** (`-L 1`): Top-level overview
2. **tasks/**: Full depth - all task files
3. **projects/** (`-L 1`): Project links only
4. **journal/**: Full depth - all journal entries
5. **knowledge/** (`-L 2`): Two-level view of knowledge base
6. **people/**: Full depth - all profiles

**Why ASCII-only**:
- Ensures compatibility across different terminals
- Prevents encoding issues in git diffs
- Consistent output regardless of locale

## Integration with gptme

### Configuration

The context generation system integrates via `gptme.toml`:

```toml
context_cmd = "scripts/context.sh"
```

**Behavior**:
- Runs automatically at session start
- Output included in initial conversation context
- Provides workspace awareness without manual steps

### When Context is Generated

1. **Session Start**: Every time you run `gptme`
2. **Manual Check**: Run `./scripts/context.sh` directly
3. **Not on Resume**: Context not regenerated when resuming existing conversation

### Context Budget Considerations

The context generation system adds approximately 2-5k tokens:
- Journal entries: 1-3k tokens (depends on content)
- Workspace structure: ~500 tokens
- Git status: ~200-500 tokens
- Task status: ~300-1k tokens

**Optimization**:
- Only most recent N journal entries in full
- Older entries listed as paths only
- Tree depth limited appropriately
- Clean, structured output

## Usage Patterns

### Standard Workflow

**Automatic Context**:
```bash
gptme
```

**Manual Review**:
```bash
./scripts/context.sh | head -100
```

**Debugging**:
```bash
./scripts/context-journal.sh
./scripts/context-workspace.sh
```

### Customization

**Adjust Journal Entry Count**:

Edit `scripts/context-journal.sh`:
```bash
MAX_FULL_ENTRIES=10
```

**Modify Workspace Structure**:

Edit `scripts/context-workspace.sh` to adjust tree depth:
```bash
TREE_KNOWLEDGE="$(LANG=C tree -a -L 3 --dirsfirst --noreport ./knowledge)"
```

**Add New Components**:

1. Create new script: `scripts/context-custom.sh`
2. Make it executable: `chmod +x scripts/context-custom.sh`
3. Add to `scripts/context.sh` orchestrator

## Best Practices

### For Component Scripts

1. **UTF-8 Encoding**: Always set at start
2. **Error Handling**: Use `set -e` to exit on errors
3. **Directory Navigation**: Use pushd/popd for clean directory changes
4. **Graceful Degradation**: Handle missing directories/files
5. **Clean Output**: Use consistent markdown formatting

### For Integration

1. **Test Before Commit**: Always run `./scripts/context.sh` before committing changes
2. **Monitor Token Usage**: Keep output concise to conserve context budget
3. **Version Control**: All context scripts should be tracked in git
4. **Documentation**: Document any customizations in this file

## Troubleshooting

### Context Not Appearing

Check `gptme.toml`:
```bash
grep context_cmd gptme.toml
```

Test script manually:
```bash
./scripts/context.sh
```

Check permissions:
```bash
ls -l scripts/context*.sh
```

### Encoding Issues

Verify LANG=C in context-workspace.sh:
```bash
grep "LANG=C" scripts/context-workspace.sh
```

### Script Errors

Test each script independently:
```bash
./scripts/context-journal.sh
./scripts/context-workspace.sh
```

Enable debug mode by adding to top of script:
```bash
set -x
```

### Large Context Output

Solutions:
1. Reduce MAX_FULL_ENTRIES in context-journal.sh
2. Decrease tree depth in context-workspace.sh
3. Add filtering for large directories

## Script Locations

All context scripts are in the `scripts/` directory:
- `scripts/context.sh` - Main orchestrator
- `scripts/context-journal.sh` - Journal extraction
- `scripts/context-workspace.sh` - Workspace structure

## Example Output

A typical context generation produces:

```text
# Context Summary
Generated on: Tue Nov 19 17:00:00 UTC 2025
---
# Journal Context
Today's Journal Entry:
[Recent journal content]
# Tasks
Output of ./scripts/tasks.py status --compact command:
[Task status or note if CLI not installed]
# Workspace structure
[Tree output for key directories]
# Git
[Git status with verbose output]
```

## Related Documentation

- [Token Efficiency Patterns](token-efficiency-patterns.md) - Optimizing context usage
- [Autonomous Operation Patterns](autonomous-operation-patterns.md) - Context integration with autonomous runs
- [Journal Entry Patterns](journal-entry-patterns.md) - Journal conventions

## Future Enhancements

Potential improvements to consider:

1. **Configurable Components**: Allow enabling/disabling components via config
2. **Smart Filtering**: Detect and skip irrelevant large files
3. **Summary Mode**: Generate compressed summaries for very large workspaces
4. **Cache Support**: Cache static parts for faster generation
5. **Rich Formatting**: Add color/highlighting for terminal output

## Maintenance

**Regular Tasks**:
- Review output periodically for token efficiency
- Update MAX_FULL_ENTRIES based on workspace size
- Adjust tree depths as knowledge base grows
- Test after script modifications

**When to Update**:
- Adding new workspace directories
- Changing journal conventions
- Integrating new tools
- Optimizing for token usage
