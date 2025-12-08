# Subagents in the Claude Agent SDK

> **Source**: https://platform.claude.com/docs/en/agent-sdk/subagents
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Overview

Subagents are specialized AIs orchestrated by the main agent, providing context management and parallelization capabilities.

## Definition Methods

1. **Programmatically** - Using `agents` parameter in `query()` options
2. **Filesystem-based** - Markdown files in `.claude/agents/` directories

## Benefits

- **Context Management**: Separate context prevents information overload
- **Parallelization**: Multiple subagents run concurrently
- **Specialized Instructions**: Tailored system prompts
- **Tool Restrictions**: Limited tools reduce unintended actions

## Creating Subagents

### Programmatic Definition

```typescript
import { query } from '@anthropic-ai/claude-agent-sdk';

const result = query({
  prompt: "Review the authentication module",
  options: {
    agents: {
      'code-reviewer': {
        description: 'Expert code review specialist',
        prompt: `You are a code review specialist.
Identify security vulnerabilities.
Check for performance issues.
Suggest improvements.`,
        tools: ['Read', 'Grep', 'Glob'],
        model: 'sonnet'
      },
      'test-runner': {
        description: 'Runs and analyzes test suites',
        prompt: `You are a test execution specialist.`,
        tools: ['Bash', 'Read', 'Grep'],
      }
    }
  }
});
```

### AgentDefinition Configuration

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `description` | `string` | Yes | When to use this agent |
| `prompt` | `string` | Yes | Agent's system prompt |
| `tools` | `string[]` | No | Allowed tools |
| `model` | `'sonnet' \| 'opus' \| 'haiku'` | No | Model override |

### Filesystem-Based Definition

Place markdown files in `.claude/agents/*.md`:

```markdown
---
name: code-reviewer
description: Expert code review specialist
tools: Read, Grep, Glob
---

Your subagent's system prompt goes here.
```

## Tool Restrictions

**Read-only agents**:
```typescript
tools: ['Read', 'Grep', 'Glob']
```

**Test execution agents**:
```typescript
tools: ['Bash', 'Read', 'Grep']
```

**Code modification agents**:
```typescript
tools: ['Read', 'Edit', 'Write', 'Grep', 'Glob']
```

## Related Documentation

- [SDK Overview](overview.md)
- [TypeScript SDK Reference](sdk-typescript.md)
- [Python SDK Reference](sdk-python.md)
- [Permissions](permissions.md)
