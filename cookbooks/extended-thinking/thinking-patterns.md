# Extended Thinking Patterns

> **Source**: [Anthropic Cookbook - Extended Thinking](https://github.com/anthropics/anthropic-cookbook/tree/main/extended_thinking)
> **Purpose**: Advanced reasoning techniques for complex problem-solving

## Overview

Extended thinking enables Claude to engage in deeper, more thorough reasoning for complex tasks. This feature allows Claude to "think" before responding, producing higher-quality outputs for challenging problems.

---

## Thinking Budget Levels

Claude Code supports multiple thinking levels through natural language triggers:

| Trigger | Token Budget | Use Case |
|---------|--------------|----------|
| `think` | Standard | Routine complex tasks |
| `think hard` | Extended | Multi-step reasoning |
| `think harder` | Deep | Complex analysis |
| `ultrathink` | Maximum | Most challenging problems |

---

## API Configuration

### Basic Extended Thinking

```python
from anthropic import Anthropic

client = Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=16000,
    thinking={
        "type": "enabled",
        "budget_tokens": 10000  # Tokens allocated for thinking
    },
    messages=[{
        "role": "user",
        "content": "Analyze this complex algorithm and suggest optimizations..."
    }]
)

# Access thinking and response separately
for block in response.content:
    if block.type == "thinking":
        print(f"Thinking:\n{block.thinking}")
    elif block.type == "text":
        print(f"Response:\n{block.text}")
```

### Streaming Extended Thinking

```python
with client.messages.stream(
    model="claude-sonnet-4-20250514",
    max_tokens=16000,
    thinking={"type": "enabled", "budget_tokens": 10000},
    messages=[{"role": "user", "content": prompt}]
) as stream:
    current_type = None
    for event in stream:
        if hasattr(event, 'type'):
            if event.type == 'content_block_start':
                current_type = event.content_block.type
                print(f"\n[{current_type.upper()}]")
            elif event.type == 'content_block_delta':
                if hasattr(event.delta, 'thinking'):
                    print(event.delta.thinking, end='', flush=True)
                elif hasattr(event.delta, 'text'):
                    print(event.delta.text, end='', flush=True)
```

---

## When to Use Extended Thinking

### Ideal Use Cases

| Category | Examples |
|----------|----------|
| **Complex Analysis** | Code review, security audits, architecture decisions |
| **Multi-Step Reasoning** | Mathematical proofs, logical deductions |
| **Creative Problem Solving** | System design, algorithm development |
| **Decision Making** | Trade-off analysis, recommendation systems |

### When NOT to Use

| Scenario | Reason |
|----------|--------|
| Simple queries | Unnecessary overhead |
| Time-critical responses | Added latency |
| Straightforward tasks | No benefit from extra reasoning |
| High-volume processing | Cost inefficiency |

---

## Thinking Budget Strategies

### Dynamic Budget Allocation

```python
def estimate_thinking_budget(task: str, complexity_indicators: list) -> int:
    """Estimate appropriate thinking budget based on task complexity."""
    base_budget = 2000

    complexity_score = 0
    for indicator in complexity_indicators:
        if indicator in task.lower():
            complexity_score += 1

    # Scale budget based on complexity
    budget_multipliers = {
        0: 1,      # 2000 tokens
        1: 2,      # 4000 tokens
        2: 3,      # 6000 tokens
        3: 4,      # 8000 tokens
        4: 5,      # 10000 tokens
    }

    multiplier = budget_multipliers.get(
        min(complexity_score, 4), 5
    )

    return base_budget * multiplier


# Usage
complexity_indicators = [
    "analyze", "compare", "evaluate", "design",
    "optimize", "debug", "refactor", "security"
]

budget = estimate_thinking_budget(
    "Analyze this codebase for security vulnerabilities",
    complexity_indicators
)  # Returns 6000+ tokens
```

### Adaptive Thinking

```python
def adaptive_thinking_call(prompt: str, client: Anthropic,
                           initial_budget: int = 4000) -> str:
    """Attempt with increasing thinking budgets if needed."""
    budgets = [initial_budget, initial_budget * 2, initial_budget * 4]

    for budget in budgets:
        response = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=16000,
            thinking={"type": "enabled", "budget_tokens": budget},
            messages=[{"role": "user", "content": prompt}]
        )

        # Check if response indicates need for more thinking
        text_response = next(
            b.text for b in response.content if b.type == "text"
        )

        if "need more analysis" not in text_response.lower():
            return text_response

    return text_response  # Return last attempt
```

---

## Combining Thinking with Tools

Extended thinking works with tool use for complex agentic tasks:

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=16000,
    thinking={"type": "enabled", "budget_tokens": 8000},
    tools=tools,
    messages=[{
        "role": "user",
        "content": """Analyze the codebase structure, identify potential
        performance bottlenecks, and suggest specific optimizations.
        Use the file_read and code_analyze tools as needed."""
    }]
)
```

### Thinking in Agentic Loops

```python
def agentic_thinking_loop(task: str, tools: list, client: Anthropic) -> str:
    """Run agentic loop with extended thinking."""
    messages = [{"role": "user", "content": task}]

    while True:
        response = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=16000,
            thinking={"type": "enabled", "budget_tokens": 6000},
            tools=tools,
            messages=messages
        )

        # Log thinking for debugging
        for block in response.content:
            if block.type == "thinking":
                print(f"[THINKING] {block.thinking[:200]}...")

        if response.stop_reason == "tool_use":
            # Process tools and continue
            tool_results = process_tools(response.content)
            messages.append({"role": "assistant", "content": response.content})
            messages.append({"role": "user", "content": tool_results})
        else:
            return next(b.text for b in response.content if b.type == "text")
```

---

## Best Practices

### Prompting for Thinking

```python
# Good: Encourage structured thinking
prompt = """
Analyze this system design for scalability issues.

Consider:
1. Current bottlenecks
2. Growth projections
3. Cost implications
4. Implementation complexity

Provide a prioritized recommendation.
"""

# Less effective: Vague request
prompt = "What do you think about this design?"
```

### Token Budget Guidelines

| Task Type | Recommended Budget | Rationale |
|-----------|-------------------|-----------|
| Code review | 6000-10000 | Multi-file analysis |
| Algorithm design | 8000-12000 | Complex reasoning |
| Security audit | 10000-16000 | Thorough examination |
| Simple analysis | 2000-4000 | Basic reasoning |

### Monitoring and Debugging

```python
def thinking_with_metrics(prompt: str, client: Anthropic,
                          budget: int) -> dict:
    """Execute with thinking and return metrics."""
    import time

    start = time.time()
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=16000,
        thinking={"type": "enabled", "budget_tokens": budget},
        messages=[{"role": "user", "content": prompt}]
    )
    duration = time.time() - start

    thinking_tokens = sum(
        len(b.thinking.split()) * 1.3  # Rough estimate
        for b in response.content if b.type == "thinking"
    )

    return {
        "response": next(b.text for b in response.content if b.type == "text"),
        "thinking_preview": next(
            (b.thinking[:500] for b in response.content if b.type == "thinking"),
            None
        ),
        "duration_seconds": duration,
        "estimated_thinking_tokens": thinking_tokens,
        "budget_utilization": thinking_tokens / budget
    }
```

---

## Common Patterns

### Chain of Thought with Thinking

```python
def chain_of_thought(problem: str, steps: list[str],
                     client: Anthropic) -> dict:
    """Execute structured chain of thought with extended thinking."""
    results = {}

    for step in steps:
        prompt = f"""
Problem: {problem}

Previous analysis: {results}

Current step: {step}

Analyze this step thoroughly.
"""
        response = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=8000,
            thinking={"type": "enabled", "budget_tokens": 4000},
            messages=[{"role": "user", "content": prompt}]
        )

        results[step] = next(
            b.text for b in response.content if b.type == "text"
        )

    return results
```

### Self-Critique Pattern

```python
def think_and_critique(task: str, client: Anthropic) -> str:
    """Generate response, then critique and improve it."""
    # Initial thinking response
    initial = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=8000,
        thinking={"type": "enabled", "budget_tokens": 6000},
        messages=[{"role": "user", "content": task}]
    )

    initial_response = next(
        b.text for b in initial.content if b.type == "text"
    )

    # Self-critique with thinking
    critique = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=8000,
        thinking={"type": "enabled", "budget_tokens": 4000},
        messages=[{
            "role": "user",
            "content": f"""Critique this response and provide an improved version:

Original task: {task}

Response to critique:
{initial_response}

Identify weaknesses and provide improved response."""
        }]
    )

    return next(b.text for b in critique.content if b.type == "text")
```

---

## Performance Considerations

| Factor | Impact | Optimization |
|--------|--------|--------------|
| Budget size | Latency & cost | Start small, increase as needed |
| Streaming | User experience | Enable for long operations |
| Caching | Repeated queries | Cache thinking for similar prompts |
| Model selection | Quality vs speed | Use thinking only when needed |
