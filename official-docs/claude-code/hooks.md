# Claude Code Hooks Reference

> **Source**: https://code.claude.com/docs/en/hooks
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Overview

Claude Code hooks are customizable checkpoints that let you intercept and control Claude's autonomous coding operations before they execute on your system.

## Hook Events

| Event | Trigger |
|-------|--------|
| `PreToolUse` | After Claude creates tool parameters but before processing the tool call |
| `PostToolUse` | Immediately after a tool completes successfully |
| `Notification` | When Claude Code sends notifications |
| `UserPromptSubmit` | When user submits a prompt, before Claude processes it |
| `Stop` | When the main Claude Code agent finishes responding |
| `SubagentStop` | When a Claude Code subagent finishes responding |
| `SessionEnd` | When a Claude Code session ends |
| `PreCompact` | Before Claude Code runs a compact operation |
| `SessionStart` | When Claude Code starts or resumes a session |

## Hook Configuration

Configure hooks in settings files:
- `~/.claude/settings.json` (user settings)
- `.claude/settings.json` (project settings)
- `.claude/settings.local.json` (local project settings)

### Example Configuration

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "command": "/path/to/your/script.py",
        "timeout": 60000
      }
    ]
  }
}
```

## Hook Input (stdin)

Hooks receive JSON data via stdin:

### Common Fields
- `session_id` - Current session identifier
- `transcript_path` - Path to conversation JSON
- `cwd` - Current working directory

### Event-Specific Fields

| Hook Event | Additional Fields |
|------------|------------------|
| `PreToolUse` | `tool_name`, `tool_input` |
| `PostToolUse` | `tool_name`, `tool_input`, `tool_response` |
| `Notification` | `message` |
| `UserPromptSubmit` | `prompt` |

## Hook Output

### Exit Codes

| Exit Code | Behavior |
|-----------|---------|
| 0 (Success) | `stdout` shown in transcript mode |
| 2 (Blocking error) | `stderr` fed back to Claude to block actions |
| Other codes | `stderr` shown to user, execution continues |

### JSON Output

| Hook | JSON Options |
|------|-------------|
| `PreToolUse` | `permissionDecision`: `"allow"`, `"deny"`, or `"ask"` |
| `PostToolUse` | `decision`: `"block"` or `undefined` |

## Example Hook Script

```python
#!/usr/bin/env python3
import sys
import json

data = json.load(sys.stdin)
tool_name = data.get('tool_name', '')
tool_input = data.get('tool_input', {})

# Block edits to sensitive files
if tool_name == 'Edit':
    file_path = tool_input.get('file_path', '')
    if '.env' in file_path:
        print(json.dumps({"permissionDecision": "deny"}))
        sys.exit(2)

sys.exit(0)
```

## Security Best Practices

1. Validate and sanitize inputs
2. Quote shell variables
3. Block path traversal
4. Use absolute paths for scripts
5. Skip sensitive files (`.env`, `.git/`)

## Related Documentation

- [Settings](settings.md)
- [CLI Reference](cli-reference.md)
