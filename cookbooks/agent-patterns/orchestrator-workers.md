# Orchestrator-Workers Pattern

> **Source**: [Anthropic Cookbook - Orchestrator Workers](https://github.com/anthropics/anthropic-cookbook/tree/main/patterns/agents)
> **Purpose**: Dynamic task decomposition with central coordination

## Overview

The orchestrator-workers workflow enables **dynamic task decomposition** where a central LLM analyzes each unique input and determines specialized subtasks for worker LLMs to execute. Unlike static parallelization, this approach adapts strategy based on context.

## Core Problem Solved

Traditional approaches require either:
- Manual prompting multiple times
- Fixed parallelization generating identical variations regardless of input

The orchestrator pattern solves this by having an orchestrator "analyze the task, determine which variations would be most valuable for this specific case, then delegate to worker LLMs."

---

## When to Use

### Appropriate Contexts

| Scenario | Example |
|----------|---------|
| Multiple distinct approaches needed | Marketing copy for different audiences |
| Optimal subtasks depend on input | Context-aware content generation |
| Strategy comparison required | A/B testing different writing styles |

### Avoid When

- Single-output tasks (unnecessary complexity)
- Latency is critical (multiple API calls add overhead)
- Subtasks are predictable and pre-definable

---

## Architecture

### Two-Phase Workflow

```
┌─────────────────┐
│   User Input    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Orchestrator  │ ◄── Phase 1: Analysis & Planning
│  (Analyze Task) │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Generate XML   │
│  Task Breakdown │
└────────┬────────┘
         │
    ┌────┴────┬────────┐
    ▼         ▼        ▼
┌───────┐ ┌───────┐ ┌───────┐
│Worker1│ │Worker2│ │Worker3│  ◄── Phase 2: Execution
└───┬───┘ └───┬───┘ └───┬───┘
    │         │        │
    └────┬────┴────────┘
         ▼
┌─────────────────┐
│  Aggregated     │
│    Results      │
└─────────────────┘
```

---

## Implementation

### XML Task Parser

```python
def parse_tasks(tasks_xml: str) -> list[dict]:
    """Parse XML tasks into structured task dictionaries."""
    tasks = []
    current_task = {}

    for line in tasks_xml.strip().split('\n'):
        line = line.strip()
        if line.startswith('<task>'):
            current_task = {}
        elif line.startswith('<type>'):
            current_task['type'] = line.replace('<type>', '').replace('</type>', '')
        elif line.startswith('<description>'):
            current_task['description'] = line.replace('<description>', '').replace('</description>', '')
        elif line.startswith('</task>'):
            if current_task:
                tasks.append(current_task)

    return tasks
```

### Prompt Templates

```python
ORCHESTRATOR_PROMPT = """
Analyze the following task and break it down into distinct approaches.
For each approach, specify a type and description.

Format your response as XML:
<tasks>
  <task>
    <type>approach-name</type>
    <description>Specific instructions for this approach</description>
  </task>
</tasks>

Task: {task}
Context: {context}
"""

WORKER_PROMPT = """
Complete the following task using the specified approach.

Original Task: {task}
Your Approach: {task_type}
Specific Instructions: {task_description}

Provide your response:
"""
```

### FlexibleOrchestrator Class

```python
class FlexibleOrchestrator:
    def __init__(self, orchestrator_prompt: str, worker_prompt: str):
        self.orchestrator_prompt = orchestrator_prompt
        self.worker_prompt = worker_prompt

    def orchestrate(self, task: str, context: str = "") -> list[dict]:
        # Phase 1: Get task breakdown from orchestrator
        prompt = self.orchestrator_prompt.format(task=task, context=context)
        response = llm_call(prompt)
        tasks = parse_tasks(extract_xml(response, "tasks"))

        # Phase 2: Execute workers (can be parallelized)
        results = []
        for t in tasks:
            worker_response = self.execute_worker(task, t)
            results.append({
                "type": t["type"],
                "description": t["description"],
                "result": worker_response
            })

        return results

    def execute_worker(self, original_task: str, task_spec: dict) -> str:
        prompt = self.worker_prompt.format(
            task=original_task,
            task_type=task_spec["type"],
            task_description=task_spec["description"]
        )
        response = llm_call(prompt)

        if not response.strip():
            print(f"Warning: Worker {task_spec['type']} returned no content")
            return "[Error: Empty response]"

        return response
```

---

## Practical Example: Marketing Copy Generation

### Input Task

```
Generate product descriptions for an eco-friendly water bottle
targeting different marketing channels.
```

### Orchestrator Output

```xml
<tasks>
  <task>
    <type>technical-specifications</type>
    <description>Feature-focused with materials, certifications,
    and measurable sustainability metrics</description>
  </task>
  <task>
    <type>lifestyle-emotional</type>
    <description>Story-driven content connecting to values
    and personal identity</description>
  </task>
  <task>
    <type>benefit-practical</type>
    <description>Problem-solution focused with everyday
    usability integration</description>
  </task>
</tasks>
```

### Worker Results

| Worker | Channel | Focus |
|--------|---------|-------|
| Technical | E-commerce | Materials, specs, certifications |
| Lifestyle | Social Media | Story, values, identity |
| Practical | Retail | Problem-solution, daily use |

---

## Performance Optimization

### Cost and Latency

- Requires **N+1 LLM calls** (1 orchestrator + N workers)
- Sequential processing in baseline implementation
- Can be parallelized with async or thread pools

### Parallel Worker Execution

```python
from concurrent.futures import ThreadPoolExecutor

def orchestrate_parallel(self, task: str, context: str = "") -> list[dict]:
    prompt = self.orchestrator_prompt.format(task=task, context=context)
    response = llm_call(prompt)
    tasks = parse_tasks(extract_xml(response, "tasks"))

    with ThreadPoolExecutor(max_workers=len(tasks)) as executor:
        futures = [
            executor.submit(self.execute_worker, task, t)
            for t in tasks
        ]
        results = [f.result() for f in futures]

    return [
        {"type": t["type"], "result": r}
        for t, r in zip(tasks, results)
    ]
```

### Model Selection Strategy

| Role | Model | Rationale |
|------|-------|-----------|
| Orchestrator | Opus/Sonnet | Complex task analysis |
| Workers | Haiku/Sonnet | Faster, cost-effective execution |

---

## Failure Modes and Mitigations

| Issue | Mitigation |
|-------|------------|
| Suboptimal decomposition | Enhanced prompt engineering with examples |
| Empty/malformed responses | Built-in validation and error handling |
| XML parsing failures | Consider JSON as alternative format |
| High API costs | Strategic model selection by role |

---

## Enhancement Opportunities

1. **Retry Logic**: Automatic retry for failed workers
2. **Synthesis Phase**: Combine worker outputs into unified result
3. **Adaptive Workers**: Adjust worker count based on task complexity
4. **Caching**: Cache orchestrator decisions for similar tasks
5. **Feedback Loop**: Use evaluator to score and improve outputs
