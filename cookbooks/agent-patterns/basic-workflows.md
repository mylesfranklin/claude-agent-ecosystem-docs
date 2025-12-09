# Basic Agent Workflow Patterns

> **Source**: [Anthropic Cookbook - Agent Patterns](https://github.com/anthropics/anthropic-cookbook/tree/main/patterns/agents)
> **Purpose**: Core architectural patterns for multi-LLM orchestration

## Overview

Three fundamental patterns for orchestrating multiple language model calls to optimize cost, latency, or task performance through decomposition and specialization.

---

## Pattern 1: Prompt Chaining

Sequential decomposition of complex tasks where each stage processes the output of the previous step.

### Core Implementation

```python
def chain(input: str, prompts: list[str]) -> str:
    """Chain multiple LLM calls sequentially, passing results between steps."""
    result = input
    for i, prompt in enumerate(prompts, 1):
        print(f"Step {i}:")
        result = llm_call(f"{prompt}\nInput: {result}")
        print(result)
    return result
```

### Example: Data Processing Pipeline

Four-step pipeline transforming raw performance data:

1. **Extraction**: Extract numerical values and associated metrics
2. **Normalization**: Convert values to standardized formats (percentages)
3. **Organization**: Sort by numerical value in descending order
4. **Formatting**: Convert to markdown table structure

### When to Use

- Complex transformations requiring progressive refinement
- Tasks needing verifiable intermediate outputs
- Debugging-friendly workflows where each stage can be validated

### Advantages

- Clear task decomposition
- Verifiable intermediate outputs
- Easier debugging and validation
- Natural for progressive refinement tasks

---

## Pattern 2: Parallelization

Process independent subtasks concurrently using multiple worker threads.

### Core Implementation

```python
from concurrent.futures import ThreadPoolExecutor

def parallel(prompt: str, inputs: list[str], n_workers: int = 3) -> list[str]:
    """Process multiple inputs concurrently with the same prompt."""
    with ThreadPoolExecutor(max_workers=n_workers) as executor:
        futures = [executor.submit(llm_call, f"{prompt}\nInput: {x}")
                   for x in inputs]
        return [f.result() for f in futures]
```

### Example: Stakeholder Impact Analysis

Process four independent perspectives simultaneously:
- **Customers**: Price sensitivity, technology demands, environmental concerns
- **Employees**: Job security, skills development, strategic direction
- **Investors**: Financial performance, risk management, transparency
- **Suppliers**: Capacity constraints, pricing pressures, technology transitions

### When to Use

- Embarrassingly parallel tasks without interdependencies
- Need to reduce wall-clock execution time
- Tasks with identical prompt structure but different inputs

### Advantages

- Significantly reduced latency
- Configurable worker pool size
- Results collected in order matching input sequence
- Optimal for independent parallel operations

---

## Pattern 3: Routing (Conditional Selection)

Dynamically select specialized processing paths based on input classification.

### Core Implementation

```python
def route(input: str, routes: dict[str, str]) -> str:
    """Route input to specialized prompt using content classification."""
    selector_prompt = f"""
    Analyze the input and select from: {list(routes.keys())}
    Provide reasoning and selection in XML format:
    <reasoning>Brief explanation</reasoning>
    <selection>Team name</selection>
    Input: {input}""".strip()

    route_response = llm_call(selector_prompt)
    reasoning = extract_xml(route_response, "reasoning")
    route_key = extract_xml(route_response, "selection").strip().lower()

    selected_prompt = routes[route_key]
    return llm_call(f"{selected_prompt}\nInput: {input}")
```

### Example: Customer Support Triage

Route tickets to specialized queues:

| Route | Focus | Tone |
|-------|-------|------|
| **Account** | Password resets, identity verification | Serious, security-focused |
| **Billing** | Charges, refunds, plan discrepancies | Professional, financial |
| **Technical** | Troubleshooting, system requirements | Detailed, step-by-step |
| **Product** | Feature education, best practices | Encouraging, educational |

### Classification Process

1. Analyze ticket content for domain indicators
2. Evaluate urgency and primary concern type
3. Generate reasoning explaining selection
4. Route to appropriate specialized prompt

### When to Use

- Diverse request types requiring specialized handling
- Need to optimize response quality per domain
- Reduce prompt complexity by splitting concerns

### Advantages

- Specialized prompts optimize response quality
- Reduced complexity per route
- Consistent handling within categories
- Minimal routing overhead

---

## Pattern Comparison

| Pattern | Primary Benefit | Cost Impact | Latency Impact |
|---------|-----------------|-------------|----------------|
| **Chaining** | Task clarity | Serial = higher cost | Sequential overhead |
| **Parallelization** | Reduced latency | Multiple concurrent calls | Significantly reduced |
| **Routing** | Response quality | Routing overhead minimal | Slight classification delay |

---

## Utility Functions Required

All patterns require these helper functions:

```python
def llm_call(prompt: str) -> str:
    """Execute LLM inference with the given prompt."""
    # Implementation using Anthropic API
    pass

def extract_xml(text: str, tag: str) -> str:
    """Parse XML-formatted responses to extract specific tags."""
    import re
    match = re.search(f'<{tag}>(.*?)</{tag}>', text, re.DOTALL)
    return match.group(1) if match else ""
```

---

## Production Considerations

- Add error handling and retry logic
- Implement caching for repeated calls
- Add monitoring and logging
- Consider rate limiting for parallel calls
- Use async patterns for better performance
