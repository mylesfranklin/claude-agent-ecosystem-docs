# Claude Agent Research Lab

> **Mission**: Push the limits of Claude Code and create industry-changing agents
> **Philosophy**: Compile the best research in the world as a reference point for all projects
> **Status**: Active Research Repository

## Vision

This repository serves as the **home base** for advanced Claude Code development. We believe Opus 4.5 combined with Claude Skills has unlocked everything the agent harness has been working toward. This synergy of systems enables the creation of agents so sophisticated that their capabilities will surprise even experienced developers.

**This is not an app repository.** It's a research lab - a carefully curated collection of the most powerful patterns, techniques, and frameworks for building with Claude.

---

## Quick Navigation

### Core Documentation

| Resource | Description | Start Here If... |
|----------|-------------|------------------|
| [Quick Start Guide](consolidated-references/quick-start-guide.md) | 5-minute setup | You're new to Claude Code |
| [Ecosystem Overview](consolidated-references/ecosystem-overview.md) | Complete landscape | You want the big picture |
| [Advanced Patterns](consolidated-references/advanced-patterns.md) | Complex architectures | You're building production systems |

---

## Repository Structure

```
claude-agent-ecosystem-docs/
│
├── 📚 official-docs/              # Anthropic official documentation
│   ├── claude-code/               # CLI, hooks, MCP, workflows
│   ├── agent-sdk/                 # TypeScript & Python SDK
│   └── engineering-blog/          # Research papers & best practices
│
├── 🧪 cookbooks/                  # Anthropic Cookbook patterns
│   ├── agent-patterns/            # Orchestrator, evaluator, workflows
│   ├── skills/                    # Skills system documentation
│   ├── capabilities/              # RAG, classification, summarization
│   ├── tool-use/                  # Tool integration patterns
│   └── extended-thinking/         # Deep reasoning techniques
│
├── ⚡ superpowers/                 # obra/superpowers framework
│   ├── overview.md                # Framework introduction
│   ├── testing/                   # TDD, anti-patterns
│   ├── debugging/                 # Systematic debugging, defense
│   ├── collaboration/             # Planning, parallel agents
│   └── meta-skills/               # Creating new skills
│
├── 📖 consolidated-references/    # Synthesized guides
│   ├── quick-start-guide.md       # Get running fast
│   ├── ecosystem-overview.md      # Full landscape
│   ├── prompting-best-practices.md
│   ├── agent-sdk-complete-reference.md
│   └── advanced-patterns.md
│
└── 🌐 github-resources/           # Community contributions
    ├── community-frameworks/      # Claude-Flow, etc.
    └── prompting-guides/          # Best practices
```

---

## What's Inside

### Anthropic Cookbooks

Extracted and optimized from [anthropic/anthropic-cookbook](https://github.com/anthropics/anthropic-cookbook):

| Document | Description |
|----------|-------------|
| [Basic Workflows](cookbooks/agent-patterns/basic-workflows.md) | Chaining, parallelization, routing |
| [Orchestrator-Workers](cookbooks/agent-patterns/orchestrator-workers.md) | Dynamic task delegation |
| [Evaluator-Optimizer](cookbooks/agent-patterns/evaluator-optimizer.md) | Iterative refinement loops |
| [Skills System](cookbooks/skills/skills-system.md) | Document generation capabilities |
| [RAG Retrieval](cookbooks/capabilities/rag-retrieval.md) | Knowledge base integration |
| [Classification](cookbooks/capabilities/classification.md) | Smart categorization |
| [Summarization](cookbooks/capabilities/summarization.md) | Multi-document synthesis |
| [Tool Patterns](cookbooks/tool-use/tool-patterns.md) | External system integration |
| [Extended Thinking](cookbooks/extended-thinking/thinking-patterns.md) | Deep reasoning |

### Superpowers Framework

Extracted and optimized from [obra/superpowers](https://github.com/obra/superpowers):

| Document | Description |
|----------|-------------|
| [Framework Overview](superpowers/overview.md) | Philosophy and workflow |
| [Test-Driven Development](superpowers/testing/test-driven-development.md) | RED-GREEN-REFACTOR |
| [Testing Anti-Patterns](superpowers/testing/testing-anti-patterns.md) | What to avoid |
| [Systematic Debugging](superpowers/debugging/systematic-debugging.md) | Four-phase root cause analysis |
| [Defense in Depth](superpowers/debugging/defense-in-depth.md) | Multi-layer validation |
| [Brainstorming](superpowers/collaboration/brainstorming.md) | Socratic design refinement |
| [Writing Plans](superpowers/collaboration/writing-plans.md) | Task decomposition |
| [Parallel Agents](superpowers/collaboration/parallel-agents.md) | Concurrent workflows |
| [Writing Skills](superpowers/meta-skills/writing-skills.md) | Create new capabilities |

### Official Documentation

| Document | Description |
|----------|-------------|
| [Claude Code Overview](official-docs/claude-code/overview.md) | Core concepts |
| [CLI Reference](official-docs/claude-code/cli-reference.md) | Commands and flags |
| [Hooks](official-docs/claude-code/hooks.md) | Event customization |
| [MCP](official-docs/claude-code/mcp.md) | Protocol integration |
| [Agent SDK Overview](official-docs/agent-sdk/overview.md) | SDK introduction |
| [TypeScript SDK](official-docs/agent-sdk/sdk-typescript.md) | Full TS reference |
| [Python SDK](official-docs/agent-sdk/sdk-python.md) | Full Python reference |
| [Building Effective Agents](official-docs/engineering-blog/building-effective-agents.md) | Architecture guide |

---

## Key Concepts

### Thinking Tokens

Control Claude's reasoning depth:

| Token | Level | Use Case |
|-------|-------|----------|
| `think` | Standard | Routine complex tasks |
| `think hard` | Extended | Multi-step reasoning |
| `think harder` | Deep | Complex analysis |
| `ultrathink` | Maximum | Most challenging problems |

### Agent Patterns

1. **Orchestrator-Worker** - Central coordinator with specialized workers
2. **Evaluator-Optimizer** - Iterative quality improvement
3. **Parallel Dispatch** - Concurrent independent tasks
4. **Prompt Chaining** - Sequential refinement
5. **Routing** - Dynamic path selection

### Superpowers Workflow

```
Brainstorming → Planning → Parallel Execution → TDD → Review → Completion
```

---

## Essential Commands

```bash
# Install Claude Code
curl -fsSL https://claude.ai/install.sh | bash

# Start Claude Code
claude

# Initialize project context
/init

# Add MCP server
claude mcp add <name> <command>

# Use extended thinking
"think hard about this problem..."

# Dispatch to subagent
"use a subagent to handle this task..."
```

---

## Source Attribution

### Official Sources
- [code.claude.com/docs](https://code.claude.com/docs) - Claude Code documentation
- [docs.anthropic.com](https://docs.anthropic.com) - Anthropic API docs
- [github.com/anthropics/anthropic-cookbook](https://github.com/anthropics/anthropic-cookbook) - Code examples
- [anthropic.com/engineering](https://anthropic.com/engineering) - Engineering blog

### Framework Sources
- [github.com/obra/superpowers](https://github.com/obra/superpowers) - Superpowers framework

### Community Sources
- [github.com/ruvnet/claude-flow](https://github.com/ruvnet/claude-flow) - Enterprise orchestration
- [github.com/awattar/claude-code-best-practices](https://github.com/awattar/claude-code-best-practices) - Best practices
- [github.com/wesammustafa/Claude-Code-Everything-You-Need-to-Know](https://github.com/wesammustafa/Claude-Code-Everything-You-Need-to-Know) - Comprehensive guide

---

## How to Use This Repository

### For New Projects

1. Read [Ecosystem Overview](consolidated-references/ecosystem-overview.md) for context
2. Follow [Quick Start Guide](consolidated-references/quick-start-guide.md) for setup
3. Reference [Prompting Best Practices](consolidated-references/prompting-best-practices.md) for prompts
4. Use [Agent Patterns](cookbooks/agent-patterns/) for architecture decisions

### For Specific Tasks

| Task | Reference |
|------|-----------|
| Build RAG system | [RAG Retrieval](cookbooks/capabilities/rag-retrieval.md) |
| Implement tool use | [Tool Patterns](cookbooks/tool-use/tool-patterns.md) |
| Create multi-agent system | [Orchestrator-Workers](cookbooks/agent-patterns/orchestrator-workers.md) |
| Debug systematically | [Systematic Debugging](superpowers/debugging/systematic-debugging.md) |
| Plan implementation | [Writing Plans](superpowers/collaboration/writing-plans.md) |
| Write new skills | [Writing Skills](superpowers/meta-skills/writing-skills.md) |

### For Research

Browse by topic:
- **Agent Architecture**: `cookbooks/agent-patterns/`
- **Development Practices**: `superpowers/`
- **Capabilities**: `cookbooks/capabilities/`
- **Official Guidance**: `official-docs/engineering-blog/`

---

## Contributing

This repository curates the best research and patterns. Contributions should:

1. Add genuinely novel techniques or frameworks
2. Include source attribution
3. Follow the existing documentation format
4. Focus on practical, tested approaches

---

## License

This documentation repository is for reference purposes. Individual source materials retain their original licenses.

---

**Last Updated**: December 2025
**Version**: 2.0.0
