# Claude-Flow: Enterprise AI Orchestration Platform

> **Source**: https://github.com/ruvnet/claude-flow
> **Author**: ruvnet
> **Category**: Community Framework

## Overview

Claude-Flow v2.7 is an enterprise-grade AI orchestration platform combining hive-mind swarm intelligence, persistent memory, and 100+ advanced MCP tools.

## Key Features

### 25 Claude Skills

```bash
"Let's pair program on this feature"        -> pair-programming skill
"Review this PR for security issues"       -> github-code-review skill
"Use vector search to find similar code"   -> agentdb-vector-search skill
"Create a swarm to build this API"         -> swarm-orchestration skill
```

### Performance Stats
- 84.8% SWE-Bench solve rate
- 32.3% token reduction
- 2.8-4.4x speed improvement
- 96x-164x faster search (AgentDB)

## Installation

```bash
# Install Claude Code first
npm install -g @anthropic-ai/claude-code
claude --dangerously-skip-permissions

# Install Claude-Flow
npx claude-flow@alpha init --force
npx claude-flow@alpha --help
```

## Memory System

### AgentDB (96x-164x Performance Boost)

```bash
# Semantic vector search
npx claude-flow@alpha memory vector-search "user authentication flow" \
  --k 10 --threshold 0.7 --namespace backend

# Store with vector embedding
npx claude-flow@alpha memory store-vector api_design "REST endpoints" \
  --namespace backend
```

## Swarm Orchestration

```bash
# Quick task execution
npx claude-flow@alpha swarm "build REST API with authentication" --claude

# Multi-agent coordination
npx claude-flow@alpha swarm init --topology mesh --max-agents 5
npx claude-flow@alpha swarm spawn researcher "analyze API patterns"
npx claude-flow@alpha swarm spawn coder "implement endpoints"
```

### Hive-Mind for Complex Projects

```bash
npx claude-flow@alpha hive-mind wizard
npx claude-flow@alpha hive-mind spawn "build enterprise system" --claude
npx claude-flow@alpha hive-mind resume session-xxxxx
```

### Swarm vs Hive-Mind

| Feature | `swarm` | `hive-mind` |
|---------|---------|-------------|
| Best For | Quick tasks | Complex projects |
| Setup | Instant | Interactive wizard |
| Memory | Task-scoped | Project-wide SQLite |
| Sessions | Temporary | Persistent + resume |

## MCP Integration

```bash
claude mcp add claude-flow npx claude-flow@alpha mcp start
claude mcp add ruv-swarm npx ruv-swarm mcp start
```

## Common Workflows

### Pattern 1: Single Feature Development

```bash
npx claude-flow@alpha init --force
npx claude-flow@alpha hive-mind spawn "Implement authentication" --claude

# Continue same feature
npx claude-flow@alpha memory query "auth" --recent
npx claude-flow@alpha swarm "Add password reset" --continue-session
```

### Pattern 2: Multi-Feature Project

```bash
npx claude-flow@alpha init --force --project-name "my-app"

# Feature 1: Authentication
npx claude-flow@alpha hive-mind spawn "auth-system" --namespace auth --claude

# Feature 2: User management
npx claude-flow@alpha hive-mind spawn "user-mgmt" --namespace users --claude
```

## Related Resources

- [Documentation Hub](https://github.com/ruvnet/claude-flow/tree/main/docs)
- [Skills Tutorial](https://github.com/ruvnet/claude-flow/blob/main/docs/guides/skills-tutorial.md)
