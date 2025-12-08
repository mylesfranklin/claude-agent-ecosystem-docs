# Model Context Protocol (MCP) Integration

> **Source**: https://code.claude.com/docs/en/mcp
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Overview

The Model Context Protocol (MCP) is an open standard that lets Claude communicate with any tool or data source using a universal interface.

## The Problem MCP Solves

**Before MCP (NxM Problem)**:
- N AI applications x M tools = NxM custom integrations
- Unsustainable exponential growth in work

**With MCP (N+M Solution)**:
- Build one connection per app + one per tool
- Linear scaling of integration effort

## MCP Architecture: Three Core Pillars

### 1. Tools (Model-Controlled)
- Developers expose capabilities (search database, write file)
- Model decides when and how to use tools

### 2. Resources (Application-Controlled)
- Feed AI rich, structured information
- Examples: files, error logs, JSON objects

### 3. Prompts (User-Controlled)
- Function like slash commands
- Simple shortcuts trigger complex actions

## Adding MCP Servers

### Basic Command
```bash
claude mcp add <server-name> <command> [args...]
```

### Examples

```bash
# Playwright (Browser Automation)
claude mcp add playwright npx '@playwright/mcp@latest'

# Claude Flow
claude mcp add claude-flow npx claude-flow@alpha mcp start
```

### Managing MCP Servers
```bash
claude mcp list
claude mcp remove <server-name>
/mcp
```

## Popular MCP Servers

- **Playwright** - Browser automation
- **Puppeteer** - Headless browser control
- **GitHub** - Repository operations
- **Slack** - Team communication
- **Google Drive** - Document access
- **Figma** - Design file access

## Configuration File Format

In `~/.claude.json`:
```json
{
  "projects": {
    "/path/to/project": {
      "mcpServers": {
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest"]
        }
      }
    }
  }
}
```

## Related Documentation

- [Hooks](hooks.md)
- [Common Workflows](common-workflows.md)
