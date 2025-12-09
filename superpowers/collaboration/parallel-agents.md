# Dispatching Parallel Agents Skill

> **Source**: [obra/superpowers - dispatching-parallel-agents](https://github.com/obra/superpowers)
> **Purpose**: Concurrent subagent workflows for accelerated development

## Overview

Parallel agent dispatch enables **multiple subagents to work simultaneously** on independent tasks, dramatically reducing total execution time while maintaining quality through structured verification.

## When to Use Parallel Agents

| Good Fit | Poor Fit |
|----------|----------|
| Independent tasks | Sequential dependencies |
| Well-defined boundaries | Shared state requirements |
| Clear success criteria | Ambiguous requirements |
| Similar complexity tasks | One task much larger than others |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      ORCHESTRATOR AGENT                         │
│            (Plans, dispatches, and aggregates)                  │
└─────────────────────────┬───────────────────────────────────────┘
                          │
         ┌────────────────┼────────────────┐
         │                │                │
         ▼                ▼                ▼
┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│  SUBAGENT 1 │   │  SUBAGENT 2 │   │  SUBAGENT 3 │
│   Task A    │   │   Task B    │   │   Task C    │
│  (parallel) │   │  (parallel) │   │  (parallel) │
└──────┬──────┘   └──────┬──────┘   └──────┬──────┘
       │                 │                 │
       └────────────────┬┴─────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────────┐
│                    VERIFICATION PHASE                           │
│          (Tests pass, integration works, no conflicts)          │
└─────────────────────────────────────────────────────────────────┘
```

---

## Task Decomposition

### Requirements for Parallel Tasks

Each task must be:
1. **Independent**: No shared state or files
2. **Complete**: All context provided upfront
3. **Verifiable**: Clear success criteria
4. **Bounded**: Predictable scope and duration

### Good Decomposition Example

```yaml
Feature: E-commerce checkout flow

Parallel Tasks:
  - task: "Implement payment processing service"
    files: [src/services/payment.py, tests/test_payment.py]
    dependencies: None
    criteria: "Payment intents create successfully, tests pass"

  - task: "Implement shipping calculation service"
    files: [src/services/shipping.py, tests/test_shipping.py]
    dependencies: None
    criteria: "Shipping rates calculate correctly, tests pass"

  - task: "Implement inventory validation service"
    files: [src/services/inventory.py, tests/test_inventory.py]
    dependencies: None
    criteria: "Stock levels validate correctly, tests pass"

Sequential Follow-up:
  - task: "Integrate services into checkout flow"
    depends_on: [payment, shipping, inventory]
```

### Bad Decomposition Example

```yaml
# DON'T DO THIS - Tasks share files and have dependencies

Parallel Tasks:
  - task: "Implement User model"
    files: [src/models/user.py]  # Conflict!

  - task: "Add email validation to User"
    files: [src/models/user.py]  # Conflict!

  - task: "Add User to database migrations"
    depends_on: ["User model must exist first"]  # Dependency!
```

---

## Dispatch Protocol

### Task Specification Format

```markdown
## Task Assignment

**Task ID**: TASK-001
**Description**: Implement payment processing service

**Context**:
- This is part of the e-commerce checkout feature
- Other agents are implementing shipping and inventory services
- Services will be integrated after all complete

**Files to Create/Modify**:
- `src/services/payment.py` (create)
- `tests/test_payment.py` (create)

**Requirements**:
1. Create PaymentService class
2. Implement create_payment_intent(amount, currency) method
3. Implement capture_payment(intent_id) method
4. Handle failures gracefully with proper exceptions
5. Write comprehensive tests

**Success Criteria**:
- [ ] All tests pass
- [ ] No linting errors
- [ ] Code follows project style guide
- [ ] Documentation strings included

**DO NOT**:
- Modify files outside your scope
- Make assumptions about other services
- Import from other in-progress services
```

### Dispatch Command

```python
# Orchestrator code
async def dispatch_parallel_tasks(tasks: list[Task]) -> list[Result]:
    """Dispatch multiple tasks to parallel subagents."""

    # Verify tasks are truly independent
    all_files = []
    for task in tasks:
        for f in task.files:
            if f in all_files:
                raise ConflictError(f"File {f} assigned to multiple tasks")
            all_files.append(f)

    # Dispatch in parallel
    async with asyncio.TaskGroup() as group:
        futures = [
            group.create_task(dispatch_to_subagent(task))
            for task in tasks
        ]

    # Collect results
    return [f.result() for f in futures]


async def dispatch_to_subagent(task: Task) -> Result:
    """Send task to a fresh subagent."""
    subagent = create_subagent(
        context=task.context,
        constraints=task.constraints
    )

    result = await subagent.execute(task.specification)

    # Verify success criteria
    if not verify_criteria(result, task.criteria):
        raise TaskFailure(task.id, result.errors)

    return result
```

---

## Subagent Guidelines

### Fresh Context

Each subagent starts with:
- Clean working state
- Only files relevant to their task
- No knowledge of other subagent work
- Clear, complete instructions

### Isolation Rules

```
SUBAGENT ISOLATION RULES

✓ DO:
  - Work only on assigned files
  - Follow the exact specification
  - Report completion status clearly
  - Document any decisions made

✗ DON'T:
  - Modify files outside your scope
  - Make assumptions about other tasks
  - Try to "help" with related work
  - Leave uncommitted changes
```

### Communication Protocol

```yaml
Subagent Output Format:

status: completed | failed | blocked
files_modified:
  - path: src/services/payment.py
    action: created
  - path: tests/test_payment.py
    action: created
tests_run: 5
tests_passed: 5
notes: "Implemented using Stripe API pattern. See docstrings for usage."
blockers: null
```

---

## Verification Phase

After all parallel tasks complete:

### Integration Testing

```python
def verify_parallel_results(results: list[Result]) -> bool:
    """Verify all parallel work integrates correctly."""

    # 1. Check all tasks succeeded
    for result in results:
        if result.status != "completed":
            return False

    # 2. Run individual test suites
    for result in results:
        if not run_tests(result.test_files):
            return False

    # 3. Run integration tests
    if not run_integration_tests():
        return False

    # 4. Check for conflicts
    if has_merge_conflicts():
        return False

    return True
```

### Conflict Resolution

If conflicts are detected:

1. **Identify conflicting changes**
2. **Determine which task has priority**
3. **Re-run lower priority task with updated context**
4. **Re-verify integration**

---

## Example: Feature Implementation

### Orchestrator Plan

```markdown
# Feature: User Dashboard

## Phase 1: Parallel Development

### Task A: Dashboard API Endpoints
- Agent: Subagent-A
- Files: src/api/dashboard.py, tests/test_dashboard_api.py
- Time estimate: 15 minutes

### Task B: Dashboard Components
- Agent: Subagent-B
- Files: src/components/Dashboard.tsx, tests/Dashboard.test.tsx
- Time estimate: 20 minutes

### Task C: Dashboard Data Models
- Agent: Subagent-C
- Files: src/models/dashboard.py, tests/test_dashboard_models.py
- Time estimate: 10 minutes

## Phase 2: Integration (Sequential)

### Task D: Wire up components to API
- Depends on: A, B, C
- Agent: Integration-Agent
```

### Execution Timeline

```
Time    Subagent-A          Subagent-B          Subagent-C
────────────────────────────────────────────────────────────
0:00    Start API           Start Components    Start Models
0:10    [working]           [working]           Complete ✓
0:15    Complete ✓          [working]           -
0:20    -                   Complete ✓          -
────────────────────────────────────────────────────────────
0:25    Integration Phase Begins
0:35    All Tests Pass ✓
```

Total time: 35 minutes
Sequential time would be: 45+ minutes

---

## Best Practices

### Do's

| Practice | Benefit |
|----------|---------|
| Verify independence before dispatch | Prevents conflicts |
| Provide complete context | Reduces subagent confusion |
| Define clear success criteria | Enables verification |
| Use fresh subagents | Prevents context pollution |

### Don'ts

| Anti-Pattern | Risk |
|--------------|------|
| Sharing files between tasks | Merge conflicts |
| Vague task descriptions | Incorrect implementations |
| Skipping verification | Integration failures |
| Too many parallel tasks | Coordination overhead |

---

## Checklist

Before dispatching parallel agents:

- [ ] Tasks are truly independent (no shared files)
- [ ] Each task has complete context
- [ ] Success criteria are measurable
- [ ] Verification plan exists
- [ ] Fallback plan for failures exists

After parallel execution:

- [ ] All tasks completed successfully
- [ ] Individual test suites pass
- [ ] Integration tests pass
- [ ] No merge conflicts
- [ ] Code review completed
