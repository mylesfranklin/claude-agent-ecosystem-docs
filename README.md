# Claude Agent Ecosystem Documentation Repository

> **Purpose**: Comprehensive reference material for Claude agent systems
> **Created**: December 2025
> **Version**: 1.0.0

## Repository Overview

This repository contains curated, LLM-optimized documentation covering the entire Claude agent ecosystem, including official Anthropic documentation, engineering insights, and high-quality community resources.

## Quick Navigation

### Start Here
- **[Quick Start Guide](consolidated-references/quick-start-guide.md)** - Get running in 5 minutes
- **[Ecosystem Overview](consolidated-references/ecosystem-overview.md)** - Understand the landscape

### Official Documentation

#### Claude Code
| Document | Description |
|----------|-------------|
| [Overview](official-docs/claude-code/overview.md) | Core concepts and capabilities |
| [CLI Reference](official-docs/claude-code/cli-reference.md) | Commands and flags |
| [Common Workflows](official-docs/claude-code/common-workflows.md) | Task patterns |
| [Hooks](official-docs/claude-code/hooks.md) | Event system customization |
| [MCP](official-docs/claude-code/mcp.md) | Model Context Protocol integration |

#### Agent SDK
| Document | Description |
|----------|-------------|
| [Overview](official-docs/agent-sdk/overview.md) | SDK installation and core concepts |
| [TypeScript Reference](official-docs/agent-sdk/sdk-typescript.md) | Full TypeScript API reference |
| [Python Reference](official-docs/agent-sdk/sdk-python.md) | Full Python API reference |
| [Subagents](official-docs/agent-sdk/subagents.md) | Creating specialized subagents |
| [Permissions](official-docs/agent-sdk/permissions.md) | Permission modes and callbacks |

#### Engineering Blog
| Document | Description |
|----------|-------------|
| [Best Practices](official-docs/engineering-blog/best-practices-agentic-coding.md) | Proven techniques |
| [Building Effective Agents](official-docs/engineering-blog/building-effective-agents.md) | Agent architecture |
| [Multi-Agent Systems](official-docs/engineering-blog/multi-agent-research.md) | Orchestrator-worker patterns |
| [Writing Tools](official-docs/engineering-blog/writing-tools-for-agents.md) | Tool design principles |

### Consolidated References

| Document | Description | Use When |
|----------|-------------|----------|
| [Ecosystem Overview](consolidated-references/ecosystem-overview.md) | Complete landscape | Understanding the ecosystem |
| [Quick Start Guide](consolidated-references/quick-start-guide.md) | 5-minute setup | Getting started |
| [Prompting Best Practices](consolidated-references/prompting-best-practices.md) | Effective prompts | Writing better prompts |
| [Agent SDK Reference](consolidated-references/agent-sdk-complete-reference.md) | Full API docs | Building custom agents |
| [Advanced Patterns](consolidated-references/advanced-patterns.md) | Architecture patterns | Complex implementations |

### Community Resources

#### Frameworks
| Resource | Description |
|----------|-------------|
| [Claude-Flow](github-resources/community-frameworks/claude-flow.md) | Enterprise orchestration platform |

#### Guides
| Resource | Description |
|----------|-------------|
| [Best Practices Guide](github-resources/prompting-guides/claude-code-best-practices-guide.md) | Workflow patterns |
| [Comprehensive Guide](github-resources/prompting-guides/claude-code-comprehensive-guide.md) | Everything you need |

## Key Concepts Quick Reference

### Core Tools

| Tool | Purpose |
|------|---------|
| **Claude Code** | Terminal-based agentic coding assistant |
| **Agent SDK** | TypeScript/Python SDK for custom agents |
| **MCP** | Universal AI-tool communication protocol |

### Essential Commands

```bash
# Install Claude Code
curl -fsSL https://claude.ai/install.sh | bash

# Start Claude Code
claude

# Initialize project context
/init

# Add MCP server
claude mcp add <name> <command>
```

### Thinking Tokens

| Token | Computation Level |
|-------|-------------------|
| `"think"` | Standard |
| `"think hard"` | Extended |
| `"think harder"` | Deep |
| `"ultrathink"` | Maximum |

### Agent Patterns

1. **Orchestrator-Worker** - Central coordinator with specialized workers
2. **Swarm** - Distributed agents with shared goals
3. **Hive-Mind** - Queen-led coordination
4. **Pipeline** - Sequential processing stages
5. **Hierarchical** - Multi-level delegation

## Source Attribution

### Official Sources
- [code.claude.com/docs](https://code.claude.com/docs)
- [docs.anthropic.com](https://docs.anthropic.com)
- [anthropic.com/engineering](https://anthropic.com/engineering)

### Community Sources
- [github.com/ruvnet/claude-flow](https://github.com/ruvnet/claude-flow)
- [github.com/awattar/claude-code-best-practices](https://github.com/awattar/claude-code-best-practices)
- [github.com/wesammustafa/Claude-Code-Everything-You-Need-to-Know](https://github.com/wesammustafa/Claude-Code-Everything-You-Need-to-Know)

## License

This documentation repository is for reference purposes. Individual source materials retain their original licenses.

---

**Last Updated**: December 2025
