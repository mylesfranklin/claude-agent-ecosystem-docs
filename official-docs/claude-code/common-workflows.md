# Claude Code Common Workflows

> **Source**: https://code.claude.com/docs/en/common-workflows
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Core Workflow: Explore - Plan - Code - Commit

### 1. Explore
- Read relevant files/images/URLs
- Use subagents for verification
- **Do NOT code yet**

### 2. Plan
- Ask Claude to make a plan
- Use thinking tokens:
  - `"think"` - Standard reasoning
  - `"think hard"` - Extended reasoning
  - `"think harder"` - Deep reasoning
  - `"ultrathink"` - Maximum computation

### 3. Code
- Implement the solution
- Verify as you go

### 4. Commit
- Commit results
- Create pull requests

## Test-Driven Development

1. Write tests based on expected inputs/outputs
2. Run & confirm tests fail
3. Commit tests
4. Write code to pass tests
5. Commit code

## Visual Iteration Workflow

1. Provide screenshots or visual mocks
2. Implement code
3. Take screenshots
4. Iterate until match (2-3 rounds)
5. Commit

## Git Worktrees

```bash
# Create worktree
git worktree add -b feature-a ../feature-a
cd ../feature-a && claude

# Manage
git worktree list
git worktree remove ../feature-a
```

## Subagent Orchestration

```markdown
Get the **backend-engineer** to suggest changes.
Then, get the **code-reviewer** to review.
Finally, get the **backend-engineer** to fix issues.
```

## CLAUDE.md Best Practices

```markdown
# Project: MyApp

## Build Commands
- `npm run build` - Production build
- `npm run dev` - Development server
- `npm test` - Run tests

## Code Style
- Use TypeScript strict mode
- Prefer functional components

## Architecture
- `/src/components` - React components
- `/src/hooks` - Custom hooks
```

## Mode Toggle

`Shift + Tab` toggles:
- **Plan Mode**: Claude explains before executing
- **Accept Edits Mode**: Claude applies directly

## Token Management

```bash
/cost        # Monitor usage
/compact     # Reduce context
```

## Related Documentation

- [CLI Reference](cli-reference.md)
- [Hooks](hooks.md)
- [MCP](mcp.md)
