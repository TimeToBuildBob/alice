---
keywords: ["journalctl", "git log", "ls -la", "large output", "command output", "grep", "head", "tail"]
---

# Rule

Always filter shell command output before it reaches chat. Never let large unfiltered output consume tokens.

# Context

Many shell commands produce massive output (logs, directory listings, git history). Reading unfiltered output wastes tokens and makes it hard to find relevant information. Filter at source using grep, head, tail, or command-specific flags.

# Detection

You need filtering when running commands that might produce:
- Logs (journalctl, application logs)
- Directory listings (ls in /usr/bin/, /var/, etc.)
- Git history (git log without limits)
- File searches (find without filters)
- Process lists (ps aux without grep)

# Pattern

```bash
# ✅ Filter logs with grep + tail
journalctl --user -u service.service --since "10 minutes ago" --no-pager -o cat | tail -50

# ❌ Unfiltered logs (could be 100k+ lines)
journalctl --user -u service.service

# ✅ Filter directory listing
ls -la /usr/bin/ | grep python

# ❌ Unfiltered listing (thousands of files)
ls -la /usr/bin/

# ✅ Limited git log
git log --oneline --max-count=10

# ❌ Full git history (could be thousands of commits)
git log

# ✅ Filtered process list
ps aux | grep python

# ❌ All processes (hundreds of lines)
ps aux

# ✅ Compact journalctl output
journalctl -u service --no-pager -o cat

# ❌ Verbose with timestamps
journalctl -u service
```

# Anti-pattern

Never use follow mode (`-f`) as it blocks the shell:
```bash
# ❌ Blocks forever
journalctl -f
tail -f logfile

# ✅ Use time-based queries instead
journalctl --since "10 minutes ago"
tail -100 logfile
```

# Outcome

Filtering at source:
- Reduces token consumption by 10-100x
- Makes output readable and actionable
- Speeds up command execution
- Prevents context pollution

# Related

- [Token Efficiency Patterns](../../knowledge/token-efficiency-patterns.md) - Shell output management section
- [shell-heredoc.md](./shell-heredoc.md) - Multiline shell commands
