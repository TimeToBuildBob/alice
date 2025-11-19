# Task State Management

This guide explains when and how to transition tasks between states in the task management system.

## Task States

Tasks can be in one of five states:
- `new` - Task created but not yet started
- `active` - Currently being worked on
- `paused` - Started but temporarily blocked or deprioritized
- `done` - Successfully completed
- `cancelled` - Abandoned or no longer relevant

## State Definitions

### new

**Definition**: Task has been created but work hasn't started.

**Characteristics**:
- In backlog or planning stage
- Not currently being worked on
- May need more definition before starting
- Waiting for dependencies or priorities to align

**Example scenarios**:
- Task just created from a brainstorming session
- Waiting for another task to complete first
- Lower priority item in backlog
- Needs more information before starting

### active

**Definition**: Task is currently being worked on.

**Characteristics**:
- Someone is actively making progress
- Should have recent journal entries
- May span multiple sessions
- Has clear next actions

**Example scenarios**:
- Working on implementation right now
- Multi-session task with ongoing progress
- Current focus of autonomous runs
- Has recent commits/updates

**Rule**: Only mark as active when you're actually working on it. Don't pre-mark tasks as active.

### paused

**Definition**: Task was started but is temporarily blocked or deprioritized.

**Characteristics**:
- Work was begun but can't continue yet
- Has a specific blocker documented
- May resume when blocker is resolved
- Not cancelled - still intends to complete

**Example scenarios**:
- Waiting for external dependency
- Blocked by technical issue
- Temporarily deprioritized for urgent work
- Needs input from someone else
- Investigating requires new information

**Important**: Document the pause reason in the task file so it's clear why work stopped and what's needed to resume.

### done

**Definition**: Task successfully completed and verified.

**Characteristics**:
- All subtasks completed
- Success criteria met
- Work verified and tested
- Final journal entry documenting completion
- No further action needed

**Example scenarios**:
- Feature implemented and tested
- Documentation written and reviewed
- Tool installed and verified working
- Process completed successfully

**Verification checklist**:
- [ ] All subtasks checked off
- [ ] Success criteria met
- [ ] Changes committed and pushed
- [ ] Tests pass (if applicable)
- [ ] Documentation updated
- [ ] Final journal entry written

### cancelled

**Definition**: Task abandoned or determined to be no longer relevant.

**Characteristics**:
- Won't be completed
- Has documented reason for cancellation
- No further action intended
- May have been superseded by other work

**Example scenarios**:
- Requirements changed
- Duplicate of another task
- No longer needed
- Superseded by different approach
- Discovered to be infeasible

**Important**: Document cancellation reason in task file for future reference.

## State Transitions

### Common Transitions
