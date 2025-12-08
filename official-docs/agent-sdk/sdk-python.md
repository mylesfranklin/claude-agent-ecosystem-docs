# Claude Agent SDK - Python Reference

> **Source**: https://platform.claude.com/docs/en/agent-sdk/python
> **Last Updated**: December 2025
> **Category**: Official Documentation

## Installation

```bash
pip install claude-agent-sdk
```

## Choosing Between query() and ClaudeSDKClient

| Feature | `query()` | `ClaudeSDKClient` |
|---------|-----------|-------------------|
| Session | Creates new session each time | Reuses same session |
| Conversation | Single exchange | Multiple exchanges |
| Interrupts | Not supported | Supported |
| Hooks | Not supported | Supported |
| Custom Tools | Not supported | Supported |

## Core Functions

### query()

```python
async def query(
    *,
    prompt: str | AsyncIterable[dict[str, Any]],
    options: ClaudeAgentOptions | None = None
) -> AsyncIterator[Message]
```

**Example**:
```python
import asyncio
from claude_agent_sdk import query, ClaudeAgentOptions

async def main():
    options = ClaudeAgentOptions(
        system_prompt="You are an expert Python developer",
        permission_mode='acceptEdits',
        cwd="/home/user/project"
    )

    async for message in query(
        prompt="Create a Python web server",
        options=options
    ):
        print(message)

asyncio.run(main())
```

### tool()

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

## ClaudeSDKClient

```python
class ClaudeSDKClient:
    def __init__(self, options: ClaudeAgentOptions | None = None)
    async def connect(self, prompt) -> None
    async def query(self, prompt, session_id) -> None
    async def receive_messages(self) -> AsyncIterator[Message]
    async def receive_response(self) -> AsyncIterator[Message]
    async def interrupt(self) -> None
    async def disconnect(self) -> None
```

### Context Manager Usage
```python
async with ClaudeSDKClient() as client:
    await client.query("Hello Claude")
    async for message in client.receive_response():
        print(message)
```

### Continuing Conversations
```python
async with ClaudeSDKClient() as client:
    await client.query("What's the capital of France?")
    async for message in client.receive_response():
        pass

    # Follow-up - Claude remembers context
    await client.query("What's the population of that city?")
    async for message in client.receive_response():
        pass
```

## ClaudeAgentOptions

```python
@dataclass
class ClaudeAgentOptions:
    allowed_tools: list[str] = field(default_factory=list)
    system_prompt: str | None = None
    mcp_servers: dict[str, McpServerConfig] = field(default_factory=dict)
    permission_mode: PermissionMode | None = None
    max_turns: int | None = None
    disallowed_tools: list[str] = field(default_factory=list)
    model: str | None = None
    cwd: str | Path | None = None
    can_use_tool: CanUseTool | None = None
    hooks: dict[HookEvent, list[HookMatcher]] | None = None
    agents: dict[str, AgentDefinition] | None = None
```

## Permission Modes

```python
PermissionMode = Literal[
    "default",           # Standard permission behavior
    "acceptEdits",       # Auto-accept file edits
    "plan",              # Planning mode - no execution
    "bypassPermissions"  # Bypass all permission checks
]
```

## Related Documentation

- [TypeScript SDK Reference](sdk-typescript.md)
- [Subagents](subagents.md)
- [Permissions](permissions.md)
