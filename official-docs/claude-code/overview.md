# Claude Code Overview

> **Source**: https://code.claude.com/docs/en/overview
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Quick Reference

Claude Code is Anthropic's agentic coding tool that lives in your terminal, helping turn ideas into code faster than ever.

## Installation

### macOS/Linux
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### Homebrew
```bash
brew install anthropic/tap/claude-code
```

### NPM
```bash
npm install -g @anthropic-ai/claude-code
```

### Start Using
```bash
cd your-project
claude
```

## Core Capabilities

### 1. Build Features from Descriptions
- Tell Claude what you want to build in plain English
- Claude makes a plan, writes code, and ensures it works

### 2. Debug and Fix Issues
- Describe a bug or paste an error message
- Claude analyzes your codebase, identifies the problem, implements a fix

### 3. Navigate Any Codebase
- Ask anything about your team's codebase
- Maintains awareness of entire project structure
- Can find up-to-date information from the web
- With MCP, can pull from external data sources (Google Drive, Figma, Slack)

### 4. Automate Tedious Tasks
- Fix lint issues
- Resolve merge conflicts
- Write release notes
- Run from developer machines or automatically in CI

## Key Differentiators

### Works in Your Terminal
- Not another chat window or IDE
- Meets you where you already work
- Uses tools you already love

### Takes Action
- Directly edits files
- Runs commands
- Creates commits
- MCP extends capabilities to read design docs, update tickets, use custom tooling

### Unix Philosophy
Composable and scriptable:
```bash
tail -f app.log | claude -p "Slack me if you see any anomalies appear in this log stream"
```

CI integration:
```bash
claude -p "If there are new text strings, translate them into French and raise a PR for @lang-fr-team to review"
```

### Enterprise-Ready
- Use Claude API
- Host on AWS or GCP
- Enterprise-grade security, privacy, and compliance

## VS Code Extension (Beta)

For those preferring a graphical interface:
- Native IDE experience
- No terminal familiarity required
- Install from VS Code marketplace

## Related Documentation

- [Quickstart Guide](quickstart.md)
- [Common Workflows](common-workflows.md)
- [CLI Reference](cli-reference.md)
- [MCP Integration](mcp.md)
- [Hooks](hooks.md)
- [Settings](settings.md)
