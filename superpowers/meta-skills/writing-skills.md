# Writing Skills Meta-Skill

> **Source**: [obra/superpowers - writing-skills](https://github.com/obra/superpowers)
> **Purpose**: Create new skills following framework standards

## Overview

This meta-skill teaches how to create **new Superpowers skills** that integrate with the framework and maintain quality standards.

## What Makes a Good Skill

### Core Properties

| Property | Description |
|----------|-------------|
| **Single Purpose** | Does one thing well |
| **Composable** | Works with other skills |
| **Documented** | Clear instructions and examples |
| **Testable** | Can verify it works correctly |
| **Bounded** | Clear scope and limitations |

---

## Skill File Structure

### Directory Layout

```
skills/
└── my-new-skill/
    ├── SKILL.md           # Main skill definition (required)
    ├── README.md          # Human documentation (optional)
    ├── examples/          # Usage examples (optional)
    │   ├── basic.md
    │   └── advanced.md
    └── tests/             # Skill tests (recommended)
        └── test_skill.md
```

### SKILL.md Template

```markdown
# Skill: [Skill Name]

## Purpose
[One sentence describing what this skill does]

## When to Use
[Conditions that trigger this skill]

## Prerequisites
[What must be true before using this skill]

## Process

### Step 1: [Action]
[Detailed instructions]

### Step 2: [Action]
[Detailed instructions]

...

## Output
[What the skill produces]

## Verification
[How to confirm the skill worked]

## Anti-Patterns
[What NOT to do]

## Related Skills
- [skill-name]: [relationship]
```

---

## Writing the SKILL.md

### Purpose Section

Be specific and concise:

```markdown
## Purpose

✓ GOOD:
"Systematically debug runtime errors using binary search
to isolate the failing component."

✗ BAD:
"Help with debugging."
"Make debugging better."
```

### When to Use Section

Define clear triggers:

```markdown
## When to Use

Use this skill when:
- A test fails unexpectedly
- An error occurs in production
- Behavior doesn't match expectations
- You need to find a root cause

Do NOT use when:
- Writing new features (use TDD skill instead)
- Doing code review (use code-review skill)
- Error is already understood (just fix it)
```

### Process Section

Provide step-by-step instructions:

```markdown
## Process

### Step 1: Reproduce the Issue
1. Create a minimal test case
2. Run the test case
3. Verify it fails consistently
4. Document exact error message

### Step 2: Isolate the Component
1. Identify all components involved
2. Test each component in isolation
3. Use binary search to narrow down
4. Document which component fails

### Step 3: Identify Root Cause
[Continue with detailed steps...]
```

### Verification Section

Define how to confirm success:

```markdown
## Verification

This skill is complete when:
- [ ] Issue can be reproduced reliably
- [ ] Root cause is identified
- [ ] Fix addresses root cause (not symptoms)
- [ ] Regression test prevents recurrence
- [ ] All existing tests still pass
```

---

## Skill Design Patterns

### The Gate Pattern

Use checkpoints to prevent progression without verification:

```markdown
### Step 1: Reproduce
[Instructions]

**GATE**: Do not proceed until:
- [ ] Reproduction is reliable
- [ ] Error is documented

### Step 2: Isolate
[Instructions]

**GATE**: Do not proceed until:
- [ ] Component is identified
- [ ] Other components ruled out
```

### The Branch Pattern

Handle different scenarios:

```markdown
### Step 3: Choose Approach

**If** test failure:
  → Proceed to Step 4A: Debug Test

**If** runtime error:
  → Proceed to Step 4B: Debug Runtime

**If** incorrect output:
  → Proceed to Step 4C: Debug Logic
```

### The Loop Pattern

For iterative processes:

```markdown
### Step 4: Iterate

**LOOP** until root cause found:
1. Form hypothesis
2. Create test for hypothesis
3. Run test
4. **If** hypothesis confirmed → Exit loop
5. **If** hypothesis rejected → Update understanding
6. Repeat from step 1
```

---

## Example: Creating a New Skill

### Scenario

You want to create a skill for "API Documentation Writing."

### Step 1: Define Purpose

```markdown
# Skill: API Documentation

## Purpose
Generate comprehensive API documentation from code,
including endpoints, parameters, responses, and examples.
```

### Step 2: Define Triggers

```markdown
## When to Use

Use this skill when:
- New API endpoint is created
- Existing endpoint is modified
- Documentation is missing or outdated
- Preparing API for external consumers

Do NOT use when:
- Writing internal comments (use code comments)
- Creating user guides (use documentation skill)
```

### Step 3: Define Process

```markdown
## Process

### Step 1: Inventory Endpoints
1. List all endpoints in the service
2. Group by resource (users, orders, etc.)
3. Note which need documentation

### Step 2: Document Each Endpoint
For each endpoint:
1. HTTP method and path
2. Description
3. Request parameters
4. Request body schema
5. Response schemas (success and error)
6. Example request/response
7. Authentication requirements

### Step 3: Generate Examples
1. Create realistic example requests
2. Show successful responses
3. Show common error responses
4. Include curl commands

### Step 4: Review and Validate
1. Test all examples work
2. Verify schemas match code
3. Check for completeness
```

### Step 4: Add Verification

```markdown
## Verification

Documentation is complete when:
- [ ] All endpoints are documented
- [ ] All examples are tested
- [ ] Schemas match actual behavior
- [ ] No undocumented parameters
- [ ] Clear for external developers
```

---

## Testing Skills

### Test Document Format

```markdown
# Test: [skill-name]

## Setup
[Initial conditions]

## Scenario 1: [Happy Path]
**Input**: [description]
**Expected**: [outcome]
**Verification**: [how to check]

## Scenario 2: [Edge Case]
**Input**: [description]
**Expected**: [outcome]
**Verification**: [how to check]

## Scenario 3: [Error Case]
**Input**: [description]
**Expected**: [outcome]
**Verification**: [how to check]
```

### Using Subagents to Test

```markdown
## Testing Process

1. Create fresh subagent
2. Load skill into subagent
3. Present test scenario
4. Verify subagent follows process
5. Check output matches expected
6. Document any deviations
```

---

## Skill Integration

### Connecting to Framework

```markdown
## Related Skills

- **brainstorming**: Use before this skill to understand requirements
- **writing-plans**: Use after this skill to plan implementation
- **verification-before-completion**: Use to verify this skill's output
```

### Composition Example

```markdown
## Workflow Integration

This skill fits into the workflow as:

brainstorming → [THIS SKILL] → writing-plans → executing-plans
```

---

## Checklist

Before publishing a skill:

- [ ] SKILL.md follows template
- [ ] Purpose is clear and specific
- [ ] When to Use is well-defined
- [ ] Process has detailed steps
- [ ] Verification criteria exist
- [ ] Anti-patterns documented
- [ ] Examples provided
- [ ] Tested with subagent
- [ ] Related skills connected
