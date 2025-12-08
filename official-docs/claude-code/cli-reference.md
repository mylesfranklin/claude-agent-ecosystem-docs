# Claude Code CLI Reference

> **Source**: https://code.claude.com/docs/en/cli-reference
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Command Syntax

```bash
claude [options] [prompt]
```

## Built-in Slash Commands

| Command | Purpose |
|---------|---------|
| `/add-dir` | Add additional working directories |
| `/agents` | Manage custom AI subagents for specialized tasks |
| `/bug` | Report bugs (sends conversation to Anthropic) |
| `/clear` | Clear conversation history |
| `/compact [instructions]` | Compact conversation with optional focus instructions |
| `/config` | View/modify configuration |
| `/cost` | Show token usage statistics |
| `/doctor` | Checks the health of your Claude Code installation |
| `/help` | Get usage help |
| `/init` | Initialize project with CLAUDE.md guide |
| `/login` | Switch Anthropic accounts |
| `/logout` | Sign out from your Anthropic account |
| `/mcp` | Manage MCP server connections and OAuth authentication |
| `/memory` | Edit CLAUDE.md memory files |
| `/model` | Select or change the AI model |
| `/permissions` | View or update permissions |
| `/pr_comments` | View pull request comments |
| `/review` | Request code review |
| `/status` | View account and system statuses |
| `/terminal-setup` | Install Shift+Enter key binding for newlines (iTerm2 and VSCode only) |
| `/vim` | Enter vim mode for alternating insert and command modes |

## CLI Flags and Options

### Common Flags

| Flag | Description |
|------|-------------|
| `-p, --prompt` | Run Claude with a specific prompt non-interactively |
| `-h, --help` | Display help information |
| `--version` | Display version information |
| `--dangerously-skip-permissions` | Skip permission checks (use with caution) |
| `--debug` | Enable debug mode for troubleshooting |

## Custom Slash Commands

Create custom commands by placing markdown files in `.claude/commands/`:

```bash
mkdir -p .claude/commands
echo "Analyze this code for performance issues:" > .claude/commands/optimize.md
```

## Interactive Mode

### Plan Mode vs Accept Edits Mode
- Toggle between modes using `Shift + Tab`
- **Plan Mode**: Claude explains what it will do before executing
- **Accept Edits Mode**: Claude applies changes directly

### Keyboard Shortcuts
- `Shift + Enter`: Insert newline (requires terminal setup)
- `Ctrl + C`: Cancel current operation
- `Ctrl + R`: Access transcript mode

## Non-Interactive Mode

Run Claude with a prompt and exit:

```bash
claude -p "What files are in this directory?"
```

Pipe input to Claude:

```bash
cat error.log | claude -p "What's causing this error?"
```

## Environment Variables

| Variable | Purpose |
|----------|--------|
| `ANTHROPIC_API_KEY` | API key for authentication |
| `CLAUDE_PROJECT_DIR` | Override project directory detection |

## Configuration Files

- `~/.claude/settings.json` - User settings
- `.claude/settings.json` - Project settings
- `.claude/settings.local.json` - Local project settings (gitignored)
- `CLAUDE.md` - Project context and memory

## Related Documentation

- [Settings](settings.md)
- [Hooks](hooks.md)
- [MCP](mcp.md)
