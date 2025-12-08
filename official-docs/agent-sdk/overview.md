# Claude Agent SDK Overview

> **Source**: https://console.anthropic.com/docs/en/agent-sdk/overview
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Installation

### TypeScript
```bash
npm install @anthropic-ai/claude-agent-sdk
```

### Python
```bash
pip install claude-agent-sdk
```

## Why Use the Claude Agent SDK?

Built on top of the agent harness that powers Claude Code:

- **Context Management**: Automatic compaction and context management
- **Rich Tool Ecosystem**: File operations, code execution, web search, MCP extensibility
- **Advanced Permissions**: Fine-grained control over agent capabilities
- **Production Essentials**: Built-in error handling, session management, monitoring
- **Optimized Claude Integration**: Automatic prompt caching and performance optimizations

## What Can You Build?

### Coding Agents
- SRE agents that diagnose and fix production issues
- Security review bots that audit code for vulnerabilities
- Oncall engineering assistants that triage incidents
- Code review agents that enforce style and best practices

### Business Agents
- Legal assistants that review contracts and compliance
- Finance advisors that analyze reports and forecasts
- Customer support agents that resolve technical issues
- Content creation assistants for marketing teams

## Core Concepts

### Authentication

Set `ANTHROPIC_API_KEY` environment variable with key from Claude Console.

**Third-Party Providers**:
| Provider | Environment Variable |
|----------|---------------------|
| Amazon Bedrock | `CLAUDE_CODE_USE_BEDROCK=1` |
| Google Vertex AI | `CLAUDE_CODE_USE_VERTEX=1` |
| Microsoft Foundry | `CLAUDE_CODE_USE_FOUNDRY=1` |

### Full Claude Code Feature Support

| Feature | Location | Description |
|---------|----------|-------------|
| Subagents | `.claude/agents/` | Specialized agents as Markdown files |
| Agent Skills | `.claude/skills/` | Extended capabilities via `SKILL.md` files |
| Hooks | `.claude/settings.json` | Custom commands responding to tool events |
| Slash Commands | `.claude/commands/` | Custom commands as Markdown files |
| Memory | `CLAUDE.md` files | Project context maintenance |

### Tool Permissions

Control which tools your agent can use:
- `allowedTools` - Explicitly allow specific tools
- `disallowedTools` - Block specific tools
- `permissionMode` - Set overall permission strategy

## Related Documentation

- [TypeScript SDK Reference](sdk-typescript.md)
- [Python SDK Reference](sdk-python.md)
- [Subagents](subagents.md)
- [Permissions](permissions.md)
