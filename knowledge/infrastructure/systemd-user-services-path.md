# Systemd User Services PATH Configuration

**Problem**: User-installed binaries in `~/.local/bin` are not accessible to systemd user services.

**Cause**: Systemd user services don't run as login shells, so they don't source `~/.profile` or `~/.bashrc` which typically add `~/.local/bin` to PATH.

## Solution

Add an explicit `Environment=` directive to the systemd service file:

```ini
[Service]
Environment="PATH=/home/bob/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
```

## Example

For Alice's autonomous service:

```bash
# Edit service file
cat > ~/.config/systemd/user/alice-autonomous.service << 'EOF'
[Unit]
Description=Alice Autonomous AI Agent
After=network.target

[Service]
Type=oneshot
WorkingDirectory=/home/bob/alice
Environment="PATH=/home/bob/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
ExecStart=/home/bob/alice/scripts/runs/autonomous/autonomous-run.sh
StandardOutput=journal
StandardError=journal
TimeoutStartSec=3600

[Install]
WantedBy=default.target
EOF

# Reload systemd
systemctl --user daemon-reload
```

## Verification

Test that commands are accessible:

```bash
# Run with explicit PATH
PATH="/home/bob/.local/bin:$PATH" <command>

# Next systemd run will have correct PATH automatically
```

## Common Symptoms

- `uv: No such file or directory` when scripts try to use uv
- Other pipx-installed tools not found
- Scripts work in interactive shell but fail in systemd

## Related

- Task management requires `uv` from `~/.local/bin`
- All pipx-installed tools affected by this issue
- Same pattern applies to other systemd user services

## Related Issues

**Pre-commit Hooks**: Git pre-commit hooks have the same PATH issue since they don't run as login shells.

**Solution for pre-commit hooks**:

```yaml
# In .pre-commit-config.yaml
- id: validate-task-frontmatter
  entry: bash -c 'PATH="/home/bob/.local/bin:$PATH" ./scripts/precommit/validate_task_frontmatter.py "$@"' --

- id: validate-task-metadata
  entry: bash -c 'PATH="/home/bob/.local/bin:$PATH"; if [ -x "./scripts/tasks.py" ]; then ./scripts/tasks.py check; fi'
```

## Fixed

- 2025-11-20: Added PATH to alice-autonomous.service
- 2025-11-20: Added PATH to pre-commit validation hooks
- Fixed task CLI accessibility in autonomous runs and git commits
