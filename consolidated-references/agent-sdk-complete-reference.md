# Claude Agent SDK Complete Reference

> **Purpose**: Comprehensive API reference for Claude Agent SDK
> **Last Updated**: December 2025
> **Category**: Consolidated Reference

## Installation

### TypeScript/JavaScript
```bash
npm install @anthropic-ai/claude-agent-sdk
```

### Python
```bash
pip install claude-agent-sdk
```

## TypeScript SDK

### Core Function: query()

```typescript
import { query } from '@anthropic-ai/claude-agent-sdk';

const result = query({
  prompt: "Analyze this codebase",
  options: {
    model: "claude-3-opus-20240229",
    systemPrompt: "You are a code reviewer."
  }
});

for await (const message of result) {
  console.log(message);
}
```

### Options Interface

```typescript
type Options = {
  abortController?: AbortController;
  agents?: Record<string, AgentDefinition>;
  allowedTools?: string[];
  disallowedTools?: string[];
  hooks?: Partial<Record<HookEvent, HookCallbackMatcher[]>>;
  mcpServers?: Record<string, McpServerConfig>;
  model?: string;
  permissionMode?: PermissionMode;
  systemPrompt?: string;
  maxTurns?: number;
  maxThinkingTokens?: number;
  cwd?: string;
};
```

### Permission Modes

```typescript
type PermissionMode =
  | 'default'           // Asks for permission
  | 'acceptEdits'       // Auto-accepts edits
  | 'bypassPermissions' // Skip all permissions
  | 'plan';             // Planning mode only
```

### Tool Definition

```typescript
import { tool, createSdkMcpServer } from '@anthropic-ai/claude-agent-sdk';
import { z } from 'zod';

const myTool = tool(
  'search_database',
  'Search the database for records',
  { query: z.string(), limit: z.number().optional() },
  async ({ query, limit }) => {
    return { content: [{ type: 'text', text: 'Results...' }] };
  }
);

const server = createSdkMcpServer({
  name: 'my-server',
  tools: [myTool]
});
```

## Python SDK

### Basic Usage

```python
import asyncio
from claude_agent_sdk import query, ClaudeAgentOptions

async def main():
    options = ClaudeAgentOptions(
        system_prompt="You are an expert Python developer",
        permission_mode='acceptEdits'
    )

    async for message in query(
        prompt="Create a Python web server",
        options=options
    ):
        print(message)

asyncio.run(main())
```

### ClaudeSDKClient

```python
from claude_agent_sdk import ClaudeSDKClient

async with ClaudeSDKClient() as client:
    await client.query("What's the capital of France?")
    async for message in client.receive_response():
        print(message)

    # Follow-up - Claude remembers context
    await client.query("What's the population of that city?")
    async for message in client.receive_response():
        print(message)
```

### Tool Decorator

```python
from claude_agent_sdk import tool

@tool("greet", "Greet a user", {"name": str})
async def greet(args):
    return {
        "content": [{
            "type": "text",
            "text": f"Hello, {args['name']}!"
        }]
    }
```

## Agent Definition

```typescript
type AgentDefinition = {
  description: string;  // When to use this agent
  tools?: string[];     // Allowed tool names
  prompt: string;       // Agent's system prompt
  model?: 'sonnet' | 'opus' | 'haiku' | 'inherit';
};
```

## API Quick Reference

| Function | Purpose |
|----------|--------|
| `query()` | Execute agent queries |
| `tool()` | Define custom tools |
| `createSdkMcpServer()` | Create MCP server |

| Type | Description |
|------|-------------|
| `Options` | Query configuration |
| `AgentDefinition` | Custom agent spec |
| `McpServerConfig` | MCP server config |
| `PermissionMode` | Permission levels |

## Related Resources

- [Claude Code Documentation](../official-docs/claude-code/overview.md)
- [Building Effective Agents](../official-docs/engineering-blog/building-effective-agents.md)
