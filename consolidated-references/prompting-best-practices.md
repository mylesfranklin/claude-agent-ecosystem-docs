# Prompting Best Practices for Claude Agents

> **Purpose**: Comprehensive guide to effective prompting
> **Last Updated**: December 2025
> **Category**: Consolidated Reference

## Core Principles

### 1. Be Specific
**Poor**: "Fix the bug"
**Good**: "The login form throws 'undefined is not a function' when submitting with special characters in the password field. Check auth.ts"

### 2. Provide Context
Include:
- Error messages (full text)
- Expected vs actual behavior
- Relevant file paths
- Recent changes

### 3. State Intent
**Poor**: "Change this function"
**Good**: "Refactor this function to be async to avoid blocking the main thread"

### 4. Use Extended Thinking
- `"think"` - Standard reasoning
- `"think hard"` - Extended reasoning
- `"think harder"` - Deep reasoning
- `"ultrathink"` - Maximum computation

## The Explore - Plan - Code - Commit Pattern

### Phase 1: Explore
```
Before making any changes, analyze the authentication module.
- What files are involved?
- How does the current flow work?
Do NOT write any code yet.
```

### Phase 2: Plan
```
Create a detailed plan for adding OAuth support.
Think hard about this plan.
```

### Phase 3: Code
```
Implement step 1 of the plan: Create the OAuth configuration file.
```

### Phase 4: Commit
```
Commit the OAuth configuration changes with a descriptive message.
```

## Prompting for Different Tasks

### Debugging
```
I'm seeing this error:
[paste full error]

Relevant context:
- This started after [recent change]
- It only happens when [condition]

Please investigate and fix.
```

### Feature Development
```
I need to implement [feature].

Requirements:
- [Requirement 1]
- [Requirement 2]

Please plan this feature before implementing.
```

### Code Review
```
Review the changes in [file] for:
- Security vulnerabilities
- Performance issues
- Code style consistency

Be thorough but concise.
```

## Subagent Prompting

### Spawning Subagents
```
Analyze the payment processing system.
Spawn 3 subagents to accelerate:
1. One to trace the payment flow
2. One to check error handling
3. One to review test coverage
Ultrathink.
```

### Orchestrating Specialists
```
Get the **security-reviewer** to audit the authentication module.
Then get the **backend-engineer** to address any findings.
Finally, get the **qa-engineer** to write tests for the fixes.
```

## Anti-Patterns to Avoid

1. **Vague Requests**: "Make it better" -> "Improve performance by reducing database queries"
2. **Missing Context**: "Fix the error" -> "Fix the TypeError in handleSubmit at line 42"
3. **Skipping Planning**: "Implement the entire payment system" -> "Plan first, then implement step by step"
4. **Overloading**: "Add auth, database, API, and tests" -> Focus on one feature at a time

## Related Resources

- [Common Workflows](../official-docs/claude-code/common-workflows.md)
- [Advanced Patterns](advanced-patterns.md)
