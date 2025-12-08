# Claude Code: Everything You Need to Know

> **Source**: https://github.com/wesammustafa/Claude-Code-Everything-You-Need-to-Know
> **Author**: wesammustafa
> **Category**: Comprehensive Community Guide

## Understanding LLMs vs AI Tools

### LLM (Large Language Model)
- Underlying AI technology/engine
- Like a car engine - core component
- Examples: GPT-4, Claude 4, Gemini

### Products Built with LLMs

| Product | LLM | Purpose |
|---------|-----|--------|
| Claude Code | Claude | Terminal-based coding assistant |
| ChatGPT | GPT | General conversations |

## Core Workflow: Explore - Plan - Code - Commit

**1. Explore** - Read relevant files, do NOT code yet

**2. Plan** - Ask Claude to make a plan using thinking tokens:
- `"think"` - Standard
- `"think hard"` - Extended
- `"think harder"` - Deep
- `"ultrathink"` - Maximum

**3. Code** - Implement solution

**4. Commit** - Commit results, create PRs

## AI Agents System

### Git Worktrees

```bash
git worktree add -b feature-a ../feature-a
cd ../feature-a && claude
git worktree remove ../feature-a
```

### General Agents

```markdown
Analyze the implementation of the payment feature
Span 5 subagents to accelerate work
Ultrathink
```

### Agent Orchestration

```markdown
Get the **backend-engineer** to suggest changes.
Then, get the **code-reviewer** to review.
Finally, get the **backend-engineer** to fix issues.
```

## Model Context Protocol (MCP)

### The Problem: NxM Integrations
- N AI apps x M tools = NxM custom integrations
- Unsustainable exponential work

### The Solution: MCP
- Universal translator for AI
- Open standard
- N+M linear scaling

### Three Core Pillars

1. **Tools** (Model-Controlled) - Model decides when/how to use
2. **Resources** (Application-Controlled) - Rich structured information
3. **Prompts** (User-Controlled) - Slash commands for complex actions

### Installing MCP Servers

```bash
cd /path/to/project
claude mcp add playwright npx '@playwright/mcp@latest'
```

## Token Management

### Token Calculation
- `tokens = words + punctuation`
- 1 token = 0.75 words (English)
- 1,000 tokens = 750 words

### Maximizing Usage

**Session Management**:
- Use `/compact` to reduce context
- Close unused sessions
- Start fresh contexts for different work

**Token Efficiency**:
- Batch similar tasks
- Be specific in prompts
- Work on one feature at a time

## Hooks System

### Hook Events

| Event | Trigger |
|-------|--------|
| `PreToolUse` | Before tool call processing |
| `PostToolUse` | After tool completes |
| `Notification` | When notifications sent |
| `UserPromptSubmit` | Before prompt processing |
| `Stop` | When agent finishes |
| `SubagentStop` | When subagent finishes |
| `SessionEnd` | When session ends |

### Hook Output

| Exit Code | Behavior |
|-----------|---------|
| 0 (Success) | stdout shown in transcript |
| 2 (Blocking) | stderr blocks action |
| Other | stderr shown, continues |

## Related Resources

- [Official Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Model Context Protocol](https://modelcontextprotocol.io/)
