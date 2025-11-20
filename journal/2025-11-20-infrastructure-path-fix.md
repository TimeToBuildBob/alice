# Infrastructure Fix: Systemd User Services PATH

**Date**: 2025-11-20
**Time**: 11:00 UTC
**Session**: Autonomous
**Duration**: ~10 minutes

## Problem Discovered

During CASCADE workflow, noticed task CLI failing:
- `uv: No such file or directory` error
- Task status command failing in autonomous context
- Despite "Set Up Task Management CLI" being marked complete

## Root Cause Analysis

1. `uv` installed at `/home/bob/.local/bin/uv`
2. `~/.profile` correctly adds `~/.local/bin` to PATH
3. But `.profile` only sourced by login shells
4. Systemd user services don't run as login shells
5. Therefore PATH missing `~/.local/bin` in autonomous runs

## Solutions Implemented

### Systemd Service
1. Updated `~/.config/systemd/user/alice-autonomous.service`
2. Added explicit `Environment="PATH=..."` directive
3. Included `/home/bob/.local/bin` at start of PATH
4. Reloaded systemd daemon: `systemctl --user daemon-reload`

### Pre-commit Hooks
1. Updated `.pre-commit-config.yaml`
2. Modified `validate-task-frontmatter` hook entry to set PATH
3. Modified `validate-task-metadata` hook entry to set PATH
4. Verified hooks work without manual PATH setup

## Verification

- Task CLI now works with correct PATH
- Fixed validation error in task metadata (created field format)
- All 3 tasks now validate cleanly

## Knowledge Base Update

Created `knowledge/infrastructure/systemd-user-services-path.md`:
- Documents the problem pattern
- Provides solution template
- Lists common symptoms
- Includes verification steps

## Impact

- Future autonomous runs will have correct PATH
- Task management CLI will work in systemd context
- Pattern documented for other user services
- Prevents similar issues with other pipx tools

## Task Status

Checked CASCADE sources after infrastructure fix:
- PRIMARY: Task #1 still BLOCKED (requires creator interaction)
- SECONDARY: No notifications or assignments
- TERTIARY: Only active task is same BLOCKED task

Infrastructure improvements complete. Main blocker remains: interactive onboarding session needed for initial-agent-setup task.
