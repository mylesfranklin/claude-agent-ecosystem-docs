# Claude Code: Best Practices for Agentic Coding

> **Source**: https://www.anthropic.com/engineering/claude-code-best-practices
> **Category**: Engineering Blog Post

## The CLAUDE.md File

Your `CLAUDE.md` files become part of Claude's prompts. Refine them like any frequently used prompt.

### Effective Contents

1. **Frequently used commands** - Build, test, lint
2. **Code style preferences** - Naming conventions, patterns
3. **Codebase structure** - Directory organization, key files

## Core Workflow: Explore - Plan - Code - Commit

### Step 1: Explore
- Read relevant files, images, and URLs
- Use subagents for verification
- **Critical**: Do NOT code yet

### Step 2: Plan
- Ask Claude to create a detailed plan
- Use extended thinking: `think`, `think hard`, `think harder`, `ultrathink`
- Save plan for future reference

### Step 3: Code
- Implement the solution
- Verify reasonableness as you go

### Step 4: Commit
- Commit changes
- Create pull requests
- Update documentation

## Test-Driven Workflow

1. **Write tests first** based on expected inputs/outputs
2. **Run tests** - confirm they fail
3. **Commit tests** before implementation
4. **Write code** to pass tests
5. **Commit code** after all tests pass

## Visual Iteration Workflow

1. Provide screenshots or visual mocks
2. Implement code
3. Take screenshots
4. Iterate until match (2-3 rounds)
5. Commit

## Multi-Claude Workflows

### Git Worktrees

```bash
git worktree add -b feature-a ../feature-a
cd ../feature-a && claude
```

### Benefits
- Multiple branches in separate folders
- No interference between worktrees
- Run multiple Claude sessions in parallel

## Subagent Strategies

### Orchestration Example
```
Get the backend-engineer to suggest API changes.
Then get the code-reviewer to review.
Finally get the backend-engineer to address feedback.
```

## Common Pitfalls

1. **Skipping the explore phase** - Leads to poor solutions
2. **Not iterating on CLAUDE.md** - Generic instructions produce generic results
3. **Premature coding** - Planning first saves time
4. **Not using subagents** - Serial work when parallel is possible

## Related Resources

- [Building Effective Agents](building-effective-agents.md)
- [Multi-Agent Research System](multi-agent-research.md)
