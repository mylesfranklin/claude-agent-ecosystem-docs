# Claude Agent SDK Permissions

> **Source**: https://platform.claude.com/docs/en/agent-sdk/permissions
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Overview

Four complementary ways to control tool usage:

1. **Permission Modes** - Global permission behavior settings
2. **canUseTool callback** - Runtime permission handler
3. **Hooks** - Fine-grained control over tool execution
4. **Permission rules** - Declarative allow/deny rules

## Permission Flow

```
Tool Request
    |
PreToolUse Hook
    |
Check Deny Rules -> Denied -> Feedback to agent
    | (No match)
Check Allow Rules -> Allow -> Execute Tool
    | (No match)
Permission Mode?
    |-- bypassPermissions -> Allow -> Execute Tool
    |-- Other modes -> canUseTool Callback
                        |
                   Execute Tool
                        |
                   PostToolUse Hook
```

## Permission Modes

| Mode | Description |
|------|-------------|
| `default` | Standard permission behavior |
| `plan` | Planning mode - read-only tools only |
| `acceptEdits` | Auto-accept file edits |
| `bypassPermissions` | Bypass all checks (use carefully) |

### Setting Permission Mode

```typescript
import { query } from "@anthropic-ai/claude-agent-sdk";

const result = await query({
  prompt: "Help me refactor this code",
  options: {
    permissionMode: 'default'
  }
});
```

### Dynamic Mode Changes

```typescript
const q = query({
  prompt: streamInput(),
  options: { permissionMode: 'default' }
});

await q.setPermissionMode('acceptEdits');
```

## canUseTool Callback

```typescript
const result = await query({
  prompt: "Help me analyze this codebase",
  options: {
    canUseTool: async (toolName, input) => {
      console.log(`Tool Request: ${toolName}`);
      
      const approved = await getUserApproval();
      
      if (approved) {
        return {
          behavior: "allow",
          updatedInput: input
        };
      } else {
        return {
          behavior: "deny",
          message: "User denied permission"
        };
      }
    }
  }
});
```

## Permission Result Types

```typescript
type PermissionResult =
  | {
      behavior: 'allow';
      updatedInput: ToolInput;
    }
  | {
      behavior: 'deny';
      message: string;
      interrupt?: boolean;
    }
```

## Best Practices

1. **Use default mode** for controlled execution
2. **Use acceptEdits mode** when working on isolated files
3. **Avoid bypassPermissions** in production
4. **Combine modes with hooks** for fine-grained control
5. **Switch modes dynamically** based on task progress

## Related Documentation

- [SDK Overview](overview.md)
- [TypeScript SDK Reference](sdk-typescript.md)
- [Python SDK Reference](sdk-python.md)
- [Hooks Reference](../claude-code/hooks.md)
