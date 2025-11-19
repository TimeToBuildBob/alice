# Task Management CLI Setup

**Date**: 2025-11-19
**Component**: gptme-contrib task management CLI
**Status**: ✅ Operational

## Overview

The task management CLI (`scripts/tasks.py`) provides enhanced task tracking capabilities for the workspace, including status views, metadata editing, and validation.

## Installation Steps

### 1. Install uv (Astral Python Package Manager)

```bash
curl -LsSf https://astral.sh/uv/install.sh -o /tmp/uv-install.sh
bash /tmp/uv-install.sh
```

**Installation location**: `~/.local/bin/uv`

**Add to PATH** (add to ~/.bashrc or ~/.profile):
```bash
export PATH="$HOME/.local/bin:$PATH"
```

### 2. Create Symlink for Easy Access

```bash
cd /home/bob/alice
ln -s ../gptme-contrib/scripts/tasks.py scripts/tasks.py
```

This allows using `./scripts/tasks.py` instead of the full path to gptme-contrib.

### 3. Verify Installation

```bash
cd /home/bob/alice
./scripts/tasks.py --help
./scripts/tasks.py status --compact
```

## Available Commands

- `status [--compact]` - Show status of all tasks
- `list [--sort state|date]` - List tasks in table format
- `show <task-id>` - Show detailed task information
- `edit <task-id> [--set|--add|--remove <field> <value>]` - Edit task metadata
- `next` - Show highest priority active task
- `tags` - List all tags and their task counts
- `check` - Validate task integrity and relationships

## Usage Examples

### Quick Status Check
```bash
./scripts/tasks.py status --compact
```

### List All Tasks
```bash
./scripts/tasks.py list
```

### Show Task Details
```bash
./scripts/tasks.py show initial-agent-setup
```

### Edit Task Metadata
```bash
# Set task state
./scripts/tasks.py edit my-task --set state active

# Add tags
./scripts/tasks.py edit my-task --add tag feature

# Add dependency
./scripts/tasks.py edit my-task --add depends other-task
```

## Dependencies

The CLI uses inline PEP 723 dependencies managed by uv:
- click >= 8.0.0
- rich >= 13.0.0
- python-frontmatter >= 1.1.0
- tabulate >= 0.9.0

Dependencies are automatically installed on first run.

## Issues Fixed

### YAML Parsing Error
Fixed invalid YAML syntax in `tasks/first-autonomous-run.md`:
- Changed: `tags: [milestone, autonomous, @alice]`
- To: `tags: [milestone, autonomous, alice]`

The `@` symbol is not valid in unquoted YAML strings.

## Known Warnings

**Deprecation Warning**: The CLI shows a warning about `tool.uv.dev-dependencies` being deprecated. This is a workspace configuration issue that should be updated in `pyproject.toml`:
- Change: `[tool.uv.dev-dependencies]`
- To: `[dependency-groups.dev]`

This is not critical but should be addressed in a future update.

## Integration

The task CLI is now integrated into the workspace:
- Symlink: `scripts/tasks.py` → `gptme-contrib/scripts/tasks.py`
- Documentation: Referenced in TASKS.md
- Status: Ready for use in autonomous and interactive sessions

## Benefits

1. **Quick Status Views**: Fast overview of task states and progress
2. **Metadata Editing**: Easier than manual YAML editing
3. **Task Filtering**: Sort and filter tasks by various criteria
4. **Validation**: Automatic link checking and integrity validation
5. **Progress Tracking**: Visual subtask completion tracking

## Related

- TASKS.md - Task system documentation
- gptme-contrib/scripts/tasks.py - Source implementation
- pyproject.toml - Workspace dependency configuration
