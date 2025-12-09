# Brainstorming Skill

> **Source**: [obra/superpowers - brainstorming](https://github.com/obra/superpowers)
> **Purpose**: Socratic design refinement before implementation

## Overview

Brainstorming is the **first step** in the Superpowers workflow. Before writing any code, you must deeply understand the problem through structured dialogue.

## Core Principle

```
"Never write code for a problem you don't fully understand."
```

The brainstorming phase exists to:
1. Clarify ambiguous requirements
2. Surface hidden assumptions
3. Identify edge cases
4. Validate the approach before investing time
5. Build shared understanding

---

## The Socratic Method

### What It Is

Instead of accepting requirements at face value, **ask probing questions** to uncover the true needs:

```
User: "Add a login feature"

❌ Wrong Response:
"Sure, I'll implement login."
[Starts coding immediately]

✓ Right Response:
"Let me understand the requirements better:
1. What authentication methods? (email/password, OAuth, SSO?)
2. Should users stay logged in? For how long?
3. What happens on failed login attempts?
4. Do we need password reset functionality?
5. Any compliance requirements? (GDPR, SOC2)
6. Are there existing user accounts to migrate?"
```

### Why It Works

| Benefit | Description |
|---------|-------------|
| **Prevents rework** | Correct understanding before implementation |
| **Surfaces complexity** | Hidden requirements emerge through questions |
| **Builds confidence** | User knows you understand their needs |
| **Creates documentation** | Q&A serves as spec documentation |

---

## Brainstorming Protocol

### Phase 1: Initial Understanding

```markdown
## Step 1: Paraphrase the Request

"Let me make sure I understand. You want [paraphrase of request]
so that [understood goal/benefit]. Is that correct?"

## Step 2: Clarify Scope

- What is included?
- What is explicitly excluded?
- What are the boundaries?

## Step 3: Identify Stakeholders

- Who will use this?
- Who will maintain this?
- Who needs to approve this?
```

### Phase 2: Deep Dive Questions

```markdown
## Functional Requirements

- What should happen when X?
- What if the user does Y?
- How should errors be handled?
- What are the success criteria?

## Non-Functional Requirements

- Performance expectations?
- Scalability needs?
- Security requirements?
- Accessibility needs?

## Edge Cases

- What if the input is empty?
- What if there are millions of records?
- What if the network fails?
- What if the user is malicious?
```

### Phase 3: Solution Exploration

```markdown
## Approach Options

"I see a few ways to solve this:

Option A: [Approach]
- Pros: [list]
- Cons: [list]
- Best when: [conditions]

Option B: [Approach]
- Pros: [list]
- Cons: [list]
- Best when: [conditions]

Which aligns better with your goals?"

## Trade-offs

"This decision involves trade-offs:
- Speed vs. Thoroughness
- Flexibility vs. Simplicity
- Now vs. Future

Where do your priorities lie?"
```

---

## Question Categories

### Technical Questions

```markdown
## Architecture
- How does this fit into the existing system?
- What services will it interact with?
- Are there performance constraints?

## Data
- What data needs to be stored?
- What's the expected data volume?
- Are there privacy considerations?

## Integration
- What APIs need to be called?
- What events need to be emitted?
- How will other systems know about changes?
```

### Business Questions

```markdown
## Value
- What problem does this solve?
- How will success be measured?
- What's the cost of not doing this?

## Priority
- Is this blocking other work?
- What's the timeline?
- Can it be phased?

## Users
- Who is the primary user?
- What's their technical level?
- How will they discover this feature?
```

### Risk Questions

```markdown
## Failure Modes
- What happens if this fails?
- What's the rollback plan?
- How will we detect problems?

## Security
- What could go wrong security-wise?
- Who should have access?
- What data is sensitive?

## Dependencies
- What are we depending on?
- What if a dependency fails?
- Are there alternatives?
```

---

## Presenting Designs

### Digestible Chunks

Present designs in small, reviewable pieces:

```markdown
## Design Presentation Format

### 1. High-Level Overview (30 seconds to understand)

"The system will have three components:
1. User authentication service
2. Permission checker middleware
3. Session management store"

### 2. Component Details (one at a time)

"Let's start with the authentication service:
- Responsibilities: [list]
- Interfaces: [list]
- Dependencies: [list]

Does this make sense before we move to the next component?"

### 3. Integration Points

"Here's how the components connect:
[diagram or description]

Any concerns about this flow?"

### 4. Edge Cases & Error Handling

"When things go wrong:
- Scenario A → Response A
- Scenario B → Response B

Should we handle any other scenarios?"
```

### Validation Checkpoints

After each chunk:
- "Does this match your understanding?"
- "Any concerns with this approach?"
- "Should we explore alternatives?"

---

## Common Pitfalls

### Rushing to Code

```
❌ "Let me just start coding and we'll figure it out"

✓ "Let me spend 10 minutes understanding before
   spending 10 hours implementing wrong"
```

### Assuming Understanding

```
❌ "I know what you mean by 'fast'"

✓ "When you say 'fast', what response time
   would make you happy? 100ms? 1 second?"
```

### Not Challenging Assumptions

```
❌ "You want a relational database? Sure."

✓ "You mentioned a relational database.
   What data patterns are you expecting?
   Would a document store fit better for X use case?"
```

---

## Brainstorming Output

### Design Document Template

```markdown
# Feature: [Name]

## Context
[Why this feature exists]

## Requirements

### Must Have
- Requirement 1
- Requirement 2

### Should Have
- Requirement 3

### Won't Have (this iteration)
- Requirement 4

## Design

### Architecture
[High-level design]

### Components
[Component details]

### Data Model
[Schema/structures]

## Edge Cases
| Scenario | Behavior |
|----------|----------|
| Empty input | Return error |
| Network failure | Retry 3 times |

## Open Questions
- [ ] Question 1 - awaiting answer
- [x] Question 2 - answered: [answer]

## Decision Log
| Decision | Rationale | Date |
|----------|-----------|------|
| Use PostgreSQL | Team expertise | 2024-01-15 |
```

---

## Transition to Planning

Brainstorming is complete when:

- [ ] All requirements are understood
- [ ] Edge cases are identified
- [ ] Design approach is validated
- [ ] Open questions are resolved
- [ ] Stakeholders approve direction

Then proceed to: [Writing Plans](./writing-plans.md)

---

## Quick Reference

| Phase | Duration | Output |
|-------|----------|--------|
| Initial Understanding | 2-5 min | Paraphrased requirements |
| Deep Dive | 5-15 min | Clarified requirements |
| Solution Exploration | 5-10 min | Chosen approach |
| Design Presentation | 10-20 min | Validated design |
| Documentation | 5-10 min | Design document |
