---
keywords: ["workspace", "journal", "tasks", "knowledge", "file path", "relative path", "cwd", "working directory"]
---

# Rule

Always use absolute paths for workspace files. Never use relative paths.

# Context

When working with files in the workspace (/home/bob/alice/), the working directory can change during execution. Relative paths become invalid when cwd changes, leading to "file not found" errors or operations in wrong locations.

# Detection

You need absolute paths when:
- Creating/editing files in workspace (journal/, tasks/, knowledge/, state/)
- Referencing workspace files in documentation
- Using file paths across multiple tool calls
- Working directory has changed or might change

# Pattern

```bash
# ✅ Correct - Absolute paths
cat /home/bob/alice/journal/2025-11-19-topic.md
echo "content" > /home/bob/alice/state/queue-manual.md

# ❌ Wrong - Relative paths
cat journal/2025-11-19-topic.md  # Breaks when cwd changes
echo "content" > state/queue-manual.md  # Dangerous
```

```bash
# When constructing paths, use pwd or hardcode
WORKSPACE="/home/bob/alice"
cat $WORKSPACE/journal/2025-11-19.md

# Or get from environment
cd /home/bob/alice
WORKSPACE=$(pwd)
```

# Outcome

Using absolute paths ensures:
- Operations succeed regardless of current directory
- No ambiguity about target location
- Safe file operations across tool boundaries
- Reduced debugging time from path issues

# Related

- [Token Efficiency Patterns](../../knowledge/token-efficiency-patterns.md) - Path efficiency section
- [Autonomous Operation Patterns](../../knowledge/autonomous-operation-patterns.md) - Absolute paths requirement
