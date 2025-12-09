# Tool Use Patterns

> **Source**: [Anthropic Cookbook - Tool Use](https://github.com/anthropics/anthropic-cookbook/tree/main/tool_use)
> **Purpose**: External tool integration for extending Claude's capabilities

## Overview

Tool use enables Claude to interact with external systems, APIs, and functions. Key patterns include:
- Basic tool definition and invocation
- Parallel tool execution
- Vision-enhanced tools
- Memory and state management
- Automatic context compaction

---

## Basic Tool Definition

### Tool Schema Structure

```python
tools = [{
    "name": "calculator",
    "description": "Evaluate mathematical expressions. Use for any arithmetic.",
    "input_schema": {
        "type": "object",
        "properties": {
            "expression": {
                "type": "string",
                "description": "Mathematical expression (e.g., '2 + 3 * 4')"
            }
        },
        "required": ["expression"]
    }
}]
```

### Tool Invocation Flow

```python
from anthropic import Anthropic

client = Anthropic()

def process_tool_call(tool_name: str, tool_input: dict) -> str:
    """Execute tool and return result."""
    if tool_name == "calculator":
        return calculate(tool_input["expression"])
    elif tool_name == "search":
        return search(tool_input["query"])
    else:
        return f"Unknown tool: {tool_name}"


def chat_with_tools(user_message: str, tools: list) -> str:
    """Complete conversation loop with tool handling."""
    messages = [{"role": "user", "content": user_message}]

    while True:
        response = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=1024,
            tools=tools,
            messages=messages
        )

        # Check if Claude wants to use a tool
        if response.stop_reason == "tool_use":
            # Extract tool use block
            tool_use = next(
                block for block in response.content
                if block.type == "tool_use"
            )

            # Execute tool
            result = process_tool_call(tool_use.name, tool_use.input)

            # Add assistant response and tool result to messages
            messages.append({"role": "assistant", "content": response.content})
            messages.append({
                "role": "user",
                "content": [{
                    "type": "tool_result",
                    "tool_use_id": tool_use.id,
                    "content": str(result)
                }]
            })
        else:
            # Claude is done, return final response
            return response.content[0].text
```

---

## Calculator Tool Example

### Implementation

```python
import re

def calculate(expression: str) -> str:
    """Safely evaluate mathematical expression."""
    # Remove non-math characters for safety
    cleaned = re.sub(r'[^0-9+\-*/().%\s]', '', expression)

    if not cleaned:
        return "Error: Invalid expression"

    try:
        # WARNING: eval is for demo only - use safer alternative in production
        result = eval(cleaned)
        return str(result)
    except (SyntaxError, ZeroDivisionError, NameError,
            TypeError, OverflowError) as e:
        return f"Error: {str(e)}"
```

### Tool Definition

```python
calculator_tool = {
    "name": "calculator",
    "description": """Evaluate mathematical expressions.
    Supports: +, -, *, /, %, (), parentheses
    Examples: '2 + 3 * 4', '(10 + 5) / 3', '2 ** 8'""",
    "input_schema": {
        "type": "object",
        "properties": {
            "expression": {
                "type": "string",
                "description": "Mathematical expression to evaluate"
            }
        },
        "required": ["expression"]
    }
}
```

---

## Parallel Tool Execution

Execute multiple tools simultaneously when they're independent:

```python
from concurrent.futures import ThreadPoolExecutor

def execute_parallel_tools(tool_calls: list) -> list:
    """Execute multiple tool calls in parallel."""
    with ThreadPoolExecutor(max_workers=len(tool_calls)) as executor:
        futures = [
            executor.submit(process_tool_call, call.name, call.input)
            for call in tool_calls
        ]
        return [f.result() for f in futures]


def chat_with_parallel_tools(user_message: str, tools: list) -> str:
    """Handle conversations with parallel tool execution."""
    messages = [{"role": "user", "content": user_message}]

    while True:
        response = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=2048,
            tools=tools,
            messages=messages
        )

        if response.stop_reason == "tool_use":
            # Collect all tool use blocks
            tool_uses = [
                block for block in response.content
                if block.type == "tool_use"
            ]

            # Execute all tools in parallel
            results = execute_parallel_tools(tool_uses)

            # Add responses
            messages.append({"role": "assistant", "content": response.content})
            messages.append({
                "role": "user",
                "content": [
                    {
                        "type": "tool_result",
                        "tool_use_id": tool_uses[i].id,
                        "content": str(results[i])
                    }
                    for i in range(len(tool_uses))
                ]
            })
        else:
            return response.content[0].text
```

---

## Tool Choice Control

### Forcing Specific Tool Use

```python
# Force use of a specific tool
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    tools=tools,
    tool_choice={"type": "tool", "name": "calculator"},
    messages=messages
)

# Force any tool use (must use at least one)
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    tools=tools,
    tool_choice={"type": "any"},
    messages=messages
)

# Allow Claude to choose (default)
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    tools=tools,
    tool_choice={"type": "auto"},
    messages=messages
)
```

---

## Vision with Tools

Combine image analysis with tool execution:

```python
import base64

def encode_image(image_path: str) -> str:
    """Encode image to base64."""
    with open(image_path, "rb") as f:
        return base64.standard_b64encode(f.read()).decode("utf-8")


vision_tools = [
    {
        "name": "analyze_chart",
        "description": "Extract data from charts and graphs",
        "input_schema": {
            "type": "object",
            "properties": {
                "chart_type": {"type": "string"},
                "data_points": {
                    "type": "array",
                    "items": {"type": "object"}
                }
            },
            "required": ["chart_type", "data_points"]
        }
    }
]

# Message with image and tools
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=2048,
    tools=vision_tools,
    messages=[{
        "role": "user",
        "content": [
            {
                "type": "image",
                "source": {
                    "type": "base64",
                    "media_type": "image/png",
                    "data": encode_image("chart.png")
                }
            },
            {
                "type": "text",
                "text": "Extract the data from this chart"
            }
        ]
    }]
)
```

---

## Memory Tool

Implement persistent memory across conversations:

```python
class MemoryTool:
    def __init__(self):
        self.memories = {}

    def store(self, key: str, value: str) -> str:
        """Store a memory."""
        self.memories[key] = value
        return f"Stored '{key}'"

    def retrieve(self, key: str) -> str:
        """Retrieve a memory."""
        return self.memories.get(key, f"No memory found for '{key}'")

    def list_keys(self) -> str:
        """List all stored memory keys."""
        return ", ".join(self.memories.keys()) or "No memories stored"

    def get_tool_definitions(self) -> list:
        return [
            {
                "name": "memory_store",
                "description": "Store information for later retrieval",
                "input_schema": {
                    "type": "object",
                    "properties": {
                        "key": {"type": "string", "description": "Memory key"},
                        "value": {"type": "string", "description": "Value to store"}
                    },
                    "required": ["key", "value"]
                }
            },
            {
                "name": "memory_retrieve",
                "description": "Retrieve previously stored information",
                "input_schema": {
                    "type": "object",
                    "properties": {
                        "key": {"type": "string", "description": "Memory key to retrieve"}
                    },
                    "required": ["key"]
                }
            },
            {
                "name": "memory_list",
                "description": "List all stored memory keys",
                "input_schema": {
                    "type": "object",
                    "properties": {}
                }
            }
        ]
```

---

## Automatic Context Compaction

Manage conversation history to stay within context limits:

```python
def compact_context(messages: list, max_tokens: int = 50000,
                    client: Anthropic = None) -> list:
    """Compact conversation history when it gets too long."""
    # Estimate current token count (rough approximation)
    total_chars = sum(len(str(m)) for m in messages)
    estimated_tokens = total_chars // 4

    if estimated_tokens < max_tokens:
        return messages

    # Keep system message and recent messages
    system_msg = messages[0] if messages[0]["role"] == "system" else None
    recent_messages = messages[-4:]  # Keep last 2 exchanges

    # Summarize middle messages
    middle_messages = messages[1:-4] if system_msg else messages[:-4]

    summary = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": f"""Summarize this conversation history concisely:

{middle_messages}

Summary:"""
        }]
    ).content[0].text

    # Reconstruct compacted messages
    compacted = []
    if system_msg:
        compacted.append(system_msg)
    compacted.append({
        "role": "user",
        "content": f"[Previous conversation summary: {summary}]"
    })
    compacted.extend(recent_messages)

    return compacted
```

---

## Tool Search with Embeddings

When you have many tools, use embeddings to find relevant ones:

```python
def find_relevant_tools(query: str, all_tools: list,
                        embeddings_cache: dict, top_k: int = 5) -> list:
    """Find the most relevant tools for a query using embeddings."""
    import voyageai
    voyage = voyageai.Client()

    # Get query embedding
    query_embedding = voyage.embed([query], model="voyage-3").embeddings[0]

    # Calculate similarities
    similarities = []
    for tool in all_tools:
        tool_key = tool["name"]
        if tool_key not in embeddings_cache:
            tool_text = f"{tool['name']}: {tool['description']}"
            embeddings_cache[tool_key] = voyage.embed(
                [tool_text], model="voyage-3"
            ).embeddings[0]

        sim = cosine_similarity(query_embedding, embeddings_cache[tool_key])
        similarities.append((tool, sim))

    # Return top-k tools
    similarities.sort(key=lambda x: x[1], reverse=True)
    return [t for t, _ in similarities[:top_k]]
```

---

## Best Practices

### Tool Design

| Practice | Description |
|----------|-------------|
| **Clear descriptions** | Explain what, when, and how |
| **Typed parameters** | Use JSON Schema properly |
| **Examples in description** | Show expected inputs |
| **Error handling** | Return helpful error messages |

### Security

| Risk | Mitigation |
|------|------------|
| Code injection | Sanitize inputs, avoid eval |
| Credential exposure | Use environment variables |
| Rate limiting | Implement throttling |
| Data leakage | Validate tool outputs |

### Performance

| Technique | Benefit |
|-----------|---------|
| Parallel execution | Reduced latency |
| Tool caching | Faster repeated calls |
| Context compaction | Longer conversations |
| Tool filtering | Fewer tokens per request |
