# Alice

[![built using gptme](https://img.shields.io/badge/built%20using-gptme%20%F0%9F%A4%96-5151f5?style=flat)](https://github.com/ErikBjare/gptme)

The name of the agent is Alice.

This git repository is the brain of Alice. It is a workspace of their thoughts and ideas.

 - Alice will write their thoughts, plans, and ideas in this repository.
 - Alice's agent harness, including this repo, is in-progress.
 - Alice is encouraged to suggest improvements to their harness.

Information about Alice can be found in [`ABOUT.md`](./ABOUT.md), including their personality and goals.
Information about Alice's harness and architecture can be found in [`ARCHITECTURE.md`](./ARCHITECTURE.md).

## Usage

Run Alice with:

```sh
pipx install gptme

# optional (but recommended): setup pre-commit hooks
pipx install pre-commit
make install

# run Alice
./run.sh "<prompt>"
```

## Autonomous Operation

Alice can run autonomously on a schedule using the included infrastructure:

**Quick Setup**:
1. Customize `scripts/runs/autonomous/autonomous-run.sh` with your agent's details
2. Edit the prompt template in the script to match your agent's goals
3. Set up systemd timer (Linux) or cron job for scheduling
4. Monitor via logs: `journalctl --user -u agent-autonomous.service`

**See**: [`scripts/runs/autonomous/README.md`](./scripts/runs/autonomous/README.md) for complete documentation.

**Features**:
- CASCADE workflow (Loose Ends → Task Selection → Execution)
- Two-queue system (manual + generated priorities)
- Safety guardrails (GREEN/YELLOW/RED operation classification)
- Session documentation and state management
- Systemd timer templates included

## Forking

You can create a clean fork of Alice by running:

```sh
./fork.sh <path> [<agent-name>]
```

Then simply follow the instructions in the output.

## Workspace Structure

 - Alice keeps track of tasks in [`TASKS.md`](./TASKS.md)
 - Alice keeps a journal in [`./journal/`](./journal/)
 - Alice keeps a knowledge base in [`./knowledge/`](./knowledge/)
 - Alice maintains profiles of people in [`./people/`](./people/)
 - Alice manages work priorities in [`./state/`](./state/) using the two-queue system (manual + generated)
 - Alice uses scripts in [`./scripts/`](./scripts/) for context generation, task management, and automation
 - Alice can add files to [`gptme.toml`](./gptme.toml) to always include them in their context

### Key Directories

**[`state/`](./state/)**: Work queue management
- `queue-manual.md` - Manually maintained work queue with strategic context
- `queue-generated.md` - Auto-generated queue from tasks and GitHub
- See [`state/README.md`](./state/README.md) for detailed documentation

**[`scripts/`](./scripts/)**: Automation and utilities
- `context.sh` - Main context generation orchestrator
- `tasks.py` - Task management CLI (optional, from gptme-contrib)
- `runs/autonomous/` - Autonomous operation infrastructure
- See [`scripts/README.md`](./scripts/README.md) for complete documentation

**[`lessons/`](./lessons/)**: Behavioral patterns and constraints
- Prevents known failure modes through structured guidance
- See [`lessons/README.md`](./lessons/README.md) for lesson system documentation
