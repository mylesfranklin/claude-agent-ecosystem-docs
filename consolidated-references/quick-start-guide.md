# Claude Agent Ecosystem Quick Start Guide

> **Purpose**: Get up and running with Claude agents in minutes
> **Last Updated**: December 2025
> **Category**: Consolidated Reference

## 5-Minute Setup

### Step 1: Install Claude Code

**macOS/Linux**:
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Homebrew**:
```bash
brew install anthropic/tap/claude-code
```

**NPM**:
```bash
npm install -g @anthropic-ai/claude-code
```

### Step 2: Start Using

```bash
cd your-project
claude
```

### Step 3: Initialize Project Context

```bash
/init
```

## Essential Commands

| Command | Purpose |
|---------|---------|
| `/help` | Get help |
| `/init` | Initialize CLAUDE.md |
| `/clear` | Clear conversation |
| `/compact` | Reduce context size |
| `/cost` | Show token usage |
| `/model` | Change AI model |

## Key Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Shift + Tab` | Toggle Plan Mode / Accept Edits Mode |
| `Ctrl + C` | Cancel current operation |
| `Ctrl + R` | Access transcript mode |

## Extended Thinking

| Command | Computation |
|---------|------------|
| `"think"` | Standard |
| `"think hard"` | Extended |
| `"think harder"` | Deep |
| `"ultrathink"` | Maximum |

## Adding MCP Servers

```bash
claude mcp add playwright npx '@playwright/mcp@latest'
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

## Quick Reference Card

```
INSTALL:    curl -fsSL https://claude.ai/install.sh
START:      claude
INIT:       /init
HELP:       /help
COMPACT:    /compact
MODE:       Shift+Tab (Plan <-> Edit)
THINK:      "think" / "ultrathink"
MCP:        claude mcp add <name> <command>
```
