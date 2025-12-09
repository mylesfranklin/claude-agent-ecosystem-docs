# Writing Plans Skill

> **Source**: [obra/superpowers - writing-plans](https://github.com/obra/superpowers)
> **Purpose**: Create detailed implementation schedules with bite-sized tasks

## Overview

After brainstorming, the next step is to **break down the work into executable tasks**. A good plan enables autonomous execution without deviation.

## What Makes a Good Plan

```
Good Plan = Small Tasks + Clear Context + Verification Steps
```

### Characteristics

| Property | Description |
|----------|-------------|
| **Granular** | Tasks take 2-5 minutes each |
| **Complete** | All context provided per task |
| **Verifiable** | Each task has success criteria |
| **Sequential** | Dependencies are explicit |
| **Resumable** | Can pause and continue |

---

## Task Size Guidelines

### The 2-5 Minute Rule

Each task should be completable in 2-5 minutes:

```
❌ Too Large:
"Implement user authentication system"

✓ Right Size:
1. Create User model with email and password_hash fields
2. Add password hashing utility function
3. Create /register endpoint
4. Create /login endpoint
5. Add JWT token generation
6. Create authentication middleware
7. Write tests for registration
8. Write tests for login
9. Write tests for middleware
```

### Why Small Tasks?

| Benefit | Explanation |
|---------|-------------|
| **Focus** | One thing at a time |
| **Progress** | Visible advancement |
| **Recovery** | Easy to restart if interrupted |
| **Review** | Simple to verify correctness |
| **Parallelization** | Can distribute to subagents |

---

## Task Specification Format

### Complete Task Template

```markdown
## Task: [ID] - [Brief Title]

### Description
[One sentence explaining what to do]

### Context
[Why this task exists, what came before]

### Files
- Create: `path/to/new_file.py`
- Modify: `path/to/existing_file.py`

### Implementation Details
```python
# Exact code or pseudo-code to implement
def new_function():
    pass
```

### Verification
- [ ] File creates/compiles without errors
- [ ] Tests pass: `pytest tests/test_new_feature.py`
- [ ] Linting passes: `ruff check path/to/file.py`

### Dependencies
- Requires: Task-001, Task-002
- Blocks: Task-005
```

### Minimal Task Template

```markdown
## Task-003: Add password hashing utility

Create `src/utils/password.py`:
- `hash_password(plain: str) -> str` using bcrypt
- `verify_password(plain: str, hashed: str) -> bool`

Tests: `pytest tests/test_password.py`
```

---

## Planning Process

### Step 1: List All Work

```markdown
## Feature: User Authentication

### Components Needed
1. Database model
2. Password utilities
3. API endpoints
4. Middleware
5. Tests
6. Documentation
```

### Step 2: Break Down Each Component

```markdown
## 1. Database Model
- Task-001: Create User model schema
- Task-002: Create migration file
- Task-003: Run migration

## 2. Password Utilities
- Task-004: Implement hash_password
- Task-005: Implement verify_password
- Task-006: Write password utility tests

## 3. API Endpoints
- Task-007: Create /register endpoint
- Task-008: Create /login endpoint
- Task-009: Create /logout endpoint
...
```

### Step 3: Add Dependencies

```markdown
Task-001: Create User model schema
  Dependencies: None

Task-002: Create migration file
  Dependencies: Task-001

Task-003: Run migration
  Dependencies: Task-002

Task-004: Implement hash_password
  Dependencies: None (parallel to Task-001)

Task-007: Create /register endpoint
  Dependencies: Task-003, Task-004
```

### Step 4: Add Verification Steps

```markdown
Task-007: Create /register endpoint

Implementation:
- Add POST /api/register route
- Validate email format
- Check email not already registered
- Hash password
- Create user record
- Return user (without password)

Verification:
- [ ] Route responds to POST
- [ ] Invalid email returns 400
- [ ] Duplicate email returns 409
- [ ] Valid request returns 201 with user
- [ ] Password not in response
- [ ] Test passes: `pytest tests/test_register.py`
```

---

## Plan Structure

### Full Plan Document

```markdown
# Implementation Plan: User Authentication

## Overview
Implement email/password authentication for the API.

## Prerequisites
- Database is set up and accessible
- Testing framework is configured
- bcrypt is installed

## Tasks

### Phase 1: Foundation (Can run in parallel)

#### Task-001: Create User model
**Duration**: 3 minutes
**Files**: `src/models/user.py`
```python
class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    email = Column(String(255), unique=True, nullable=False)
    password_hash = Column(String(255), nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)
```
**Verify**: Model imports without error

#### Task-002: Create password utilities
**Duration**: 3 minutes
**Files**: `src/utils/password.py`
```python
import bcrypt

def hash_password(password: str) -> str:
    return bcrypt.hashpw(password.encode(), bcrypt.gensalt()).decode()

def verify_password(password: str, hashed: str) -> bool:
    return bcrypt.checkpw(password.encode(), hashed.encode())
```
**Verify**: `pytest tests/test_password.py`

### Phase 2: Database (Sequential)

#### Task-003: Create migration
**Duration**: 2 minutes
**Depends on**: Task-001
**Command**: `alembic revision --autogenerate -m "add_users_table"`
**Verify**: Migration file created

#### Task-004: Run migration
**Duration**: 1 minute
**Depends on**: Task-003
**Command**: `alembic upgrade head`
**Verify**: `alembic current` shows latest revision

### Phase 3: API Endpoints

#### Task-005: Implement /register
**Duration**: 5 minutes
**Depends on**: Task-002, Task-004
**Files**: `src/routes/auth.py`
[Implementation details...]
**Verify**: `pytest tests/test_auth.py::test_register`

...

## Completion Checklist
- [ ] All tasks completed
- [ ] All tests pass
- [ ] No linting errors
- [ ] Manual verification completed
- [ ] Ready for code review
```

---

## Execution Guidelines

### Following the Plan

```
EXECUTION RULES

1. Work through tasks IN ORDER
2. Complete verification before moving on
3. Do NOT skip tasks
4. Do NOT add unplanned work
5. If blocked, document and pause
```

### When to Deviate

Only deviate from the plan when:
- A critical bug is discovered
- A security issue is found
- The task is literally impossible as written

In these cases:
1. Document the issue
2. Propose plan amendment
3. Get approval before proceeding

### Progress Tracking

```markdown
## Progress

| Task | Status | Notes |
|------|--------|-------|
| Task-001 | ✅ Done | - |
| Task-002 | ✅ Done | - |
| Task-003 | ✅ Done | - |
| Task-004 | 🔄 In Progress | Running migration |
| Task-005 | ⏳ Pending | - |
| Task-006 | ⏳ Pending | - |
```

---

## Integration with Other Skills

### Connecting to Superpowers Workflow

```
brainstorming
    ↓
[writing-plans] ← You are here
    ↓
executing-plans
    ↓
subagent-driven-development
    ↓
verification-before-completion
```

### Handoff to Execution

When plan is complete:
1. Review plan with stakeholder
2. Get approval to proceed
3. Hand off to [executing-plans](./executing-plans.md)
4. Dispatch tasks to subagents if parallel

---

## Checklist

Before considering a plan complete:

- [ ] Every piece of work has a task
- [ ] Tasks are 2-5 minutes each
- [ ] Dependencies are explicit
- [ ] Verification steps are specific
- [ ] Parallel opportunities identified
- [ ] Plan reviewed and approved
