# Claude Agent SDK - TypeScript Reference

> **Source**: https://platform.claude.com/docs/en/agent-sdk/typescript
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Installation

```bash
npm install @anthropic-ai/claude-agent-sdk
```

## Core Functions

### query()

```typescript
function query({
  prompt,
  options
}: {
  prompt: string | AsyncIterable<SDKUserMessage>;
  options?: Options;
}): Query
```

### tool()

```typescript
function tool<Schema extends ZodRawShape>(
  name: string,
  description: string,
  inputSchema: Schema,
  handler: (args: z.infer<ZodObject<Schema>>, extra: unknown) => Promise<CallToolResult>
): SdkMcpToolDefinition<Schema>
```

### createSdkMcpServer()

```typescript
function createSdkMcpServer(options: {
  name: string;
  version?: string;
  tools?: Array<SdkMcpToolDefinition<any>>;
}): McpSdkServerConfigWithInstance
```

## Options Interface

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
}
```

## Permission Modes

```typescript
type PermissionMode =
  | 'default'           // Standard permission behavior
  | 'acceptEdits'       // Auto-accept file edits
  | 'bypassPermissions' // Bypass all permission checks
  | 'plan'              // Planning mode - no execution
```

## Agent Definition

```typescript
type AgentDefinition = {
  description: string;
  tools?: string[];
  prompt: string;
  model?: 'sonnet' | 'opus' | 'haiku' | 'inherit';
}
```

## Example Usage

### Basic Query
```typescript
import { query } from '@anthropic-ai/claude-agent-sdk';

const result = query({
  prompt: "Analyze this codebase",
  options: {
    allowedTools: ['Read', 'Grep', 'Glob'],
    permissionMode: 'default'
  }
});

for await (const message of result) {
  console.log(message);
}
```

### With Custom Tools
```typescript
import { query, tool, createSdkMcpServer } from '@anthropic-ai/claude-agent-sdk';
import { z } from 'zod';

const calculator = tool(
  'calculate',
  'Perform calculations',
  { expression: z.string() },
  async ({ expression }) => ({
    content: [{ type: 'text', text: `Result: ${eval(expression)}` }]
  })
);

const server = createSdkMcpServer({
  name: 'my-tools',
  tools: [calculator]
});

const result = query({
  prompt: "What is 2 + 2?",
  options: {
    mcpServers: { calc: server },
    allowedTools: ['mcp__calc__calculate']
  }
});
```

### With Subagents
```typescript
const result = query({
  prompt: "Review the authentication module",
  options: {
    agents: {
      'code-reviewer': {
        description: 'Expert code review specialist',
        prompt: 'You are a security-focused code reviewer...',
        tools: ['Read', 'Grep', 'Glob'],
        model: 'sonnet'
      }
    }
  }
});
```

## Related Documentation

- [Python SDK Reference](sdk-python.md)
- [Subagents](subagents.md)
- [Permissions](permissions.md)
