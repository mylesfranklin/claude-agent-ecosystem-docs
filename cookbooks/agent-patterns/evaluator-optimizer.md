# Evaluator-Optimizer Pattern

> **Source**: [Anthropic Cookbook - Evaluator Optimizer](https://github.com/anthropics/anthropic-cookbook/tree/main/patterns/agents)
> **Purpose**: Iterative refinement through evaluation feedback loops

## Overview

The evaluator-optimizer pattern implements an **iterative loop** where one LLM generates solutions while another evaluates and provides feedback until quality requirements are met.

## When to Use

| Condition | Example |
|-----------|---------|
| Clear evaluation criteria exist | Code must pass tests, content must meet style guide |
| Iterative refinement adds value | Writing, code optimization, design |
| LLM responses improve with feedback | Complex problem-solving |
| Model can provide meaningful critique | Technical or structured outputs |

---

## Architecture

```
┌─────────────────┐
│   Initial Task  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Generator     │──────────────────────┐
│  (Create/Refine)│                      │
└────────┬────────┘                      │
         │                               │
         ▼                               │
┌─────────────────┐                      │
│   Evaluator     │                      │
│  (Assess/Score) │                      │
└────────┬────────┘                      │
         │                               │
         ▼                               │
    ┌────┴────┐                          │
    │  PASS?  │───── No ─────────────────┘
    └────┬────┘      (with feedback)
         │
         │ Yes
         ▼
┌─────────────────┐
│  Final Result   │
└─────────────────┘
```

---

## Implementation

### Generator Function

```python
def generate(prompt: str, task: str, context: str = "") -> tuple[str, str]:
    """Generate and improve a solution based on feedback."""
    if context:
        full_prompt = f"{prompt}\n{context}\nTask: {task}"
    else:
        full_prompt = f"{prompt}\nTask: {task}"

    response = llm_call(full_prompt)
    thoughts = extract_xml(response, "thoughts")
    result = extract_xml(response, "response")

    return thoughts, result
```

### Evaluator Function

```python
def evaluate(prompt: str, content: str, task: str) -> tuple[str, str]:
    """Evaluate if a solution meets requirements."""
    full_prompt = f"""{prompt}
Original task: {task}
Content to evaluate: {content}"""

    response = llm_call(full_prompt)
    evaluation = extract_xml(response, "evaluation")  # "PASS" or "NEEDS_IMPROVEMENT"
    feedback = extract_xml(response, "feedback")

    return evaluation, feedback
```

### Main Loop

```python
def loop(task: str, evaluator_prompt: str, generator_prompt: str,
         max_iterations: int = 5) -> tuple[str, list[dict]]:
    """Keep generating and evaluating until requirements are met."""
    memory = []
    chain_of_thought = []

    # Initial generation
    thoughts, result = generate(generator_prompt, task)
    memory.append(result)
    chain_of_thought.append({
        "iteration": 1,
        "thoughts": thoughts,
        "result": result
    })

    iteration = 1
    while iteration < max_iterations:
        # Evaluate current result
        evaluation, feedback = evaluate(evaluator_prompt, result, task)

        if evaluation == "PASS":
            chain_of_thought[-1]["status"] = "PASSED"
            return result, chain_of_thought

        # Prepare context with history and feedback
        context = "\n".join([
            "Previous attempts:",
            *[f"- Attempt {i+1}: {m[:100]}..." for i, m in enumerate(memory)],
            f"\nFeedback on latest attempt: {feedback}"
        ])

        # Generate improved version
        iteration += 1
        thoughts, result = generate(generator_prompt, task, context)
        memory.append(result)
        chain_of_thought.append({
            "iteration": iteration,
            "thoughts": thoughts,
            "result": result,
            "previous_feedback": feedback
        })

    chain_of_thought[-1]["status"] = "MAX_ITERATIONS_REACHED"
    return result, chain_of_thought
```

---

## Prompt Templates

### Generator Prompt

```python
GENERATOR_PROMPT = """
You are a solution generator. Complete the given task to the best of your ability.
If previous attempts and feedback are provided, use them to improve your response.

Structure your response as:
<thoughts>
Your reasoning process and how you're addressing any feedback
</thoughts>
<response>
Your complete solution
</response>
"""
```

### Evaluator Prompt

```python
EVALUATOR_PROMPT = """
You are a quality evaluator. Assess the solution against these criteria:

1. Correctness: Does it solve the problem accurately?
2. Completeness: Are all requirements addressed?
3. Quality: Is it well-structured and maintainable?
4. Edge Cases: Are error conditions handled?

Structure your response as:
<evaluation>PASS or NEEDS_IMPROVEMENT</evaluation>
<feedback>
If NEEDS_IMPROVEMENT, provide specific actionable feedback.
If PASS, briefly explain why it meets all criteria.
</feedback>
"""
```

---

## Practical Example: Code Implementation

### Task: Implement MinStack

```
Implement a stack supporting push, pop, top, and O(1) minimum retrieval.
```

### Iteration 1: Initial Generation

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []

    def push(self, val):
        self.stack.append(val)
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)

    def pop(self):
        if self.stack:
            if self.stack[-1] == self.min_stack[-1]:
                self.min_stack.pop()
            return self.stack.pop()

    def top(self):
        return self.stack[-1] if self.stack else None

    def get_min(self):
        return self.min_stack[-1] if self.min_stack else None
```

### Evaluator Feedback

```
NEEDS_IMPROVEMENT

Issues:
1. Error handling: Silent failures instead of proper exceptions
2. Type hints: Missing type annotations
3. Documentation: No docstrings explaining behavior
4. Edge cases: pop() on empty stack should raise exception
```

### Iteration 2: Refined Solution

```python
class MinStack:
    """
    Stack data structure with O(1) minimum element retrieval.

    Uses auxiliary stack to track minimum values.
    """

    def __init__(self) -> None:
        self.stack: list[int] = []
        self.min_stack: list[int] = []

    def push(self, val: int) -> None:
        """Push value onto stack."""
        self.stack.append(val)
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)

    def pop(self) -> int:
        """Remove and return top element. Raises IndexError if empty."""
        if not self.stack:
            raise IndexError("pop from empty stack")

        val = self.stack.pop()
        if val == self.min_stack[-1]:
            self.min_stack.pop()
        return val

    def top(self) -> int:
        """Return top element without removal. Raises IndexError if empty."""
        if not self.stack:
            raise IndexError("top from empty stack")
        return self.stack[-1]

    def get_min(self) -> int:
        """Return minimum element in O(1). Raises IndexError if empty."""
        if not self.min_stack:
            raise IndexError("get_min from empty stack")
        return self.min_stack[-1]
```

### Final Evaluation

```
PASS

The solution now:
✓ Raises proper exceptions for error conditions
✓ Includes comprehensive type hints
✓ Has clear docstrings for all methods
✓ Maintains O(1) time complexity for all operations
```

---

## Advanced Patterns

### Multi-Criteria Evaluation

```python
def evaluate_multi_criteria(content: str, criteria: list[dict]) -> dict:
    """Evaluate against multiple weighted criteria."""
    results = {}

    for criterion in criteria:
        prompt = f"""
        Evaluate this content for: {criterion['name']}
        Weight: {criterion['weight']}
        Requirements: {criterion['requirements']}

        Content: {content}

        Score (0-100) and explain:
        """
        response = llm_call(prompt)
        results[criterion['name']] = {
            "score": extract_xml(response, "score"),
            "explanation": extract_xml(response, "explanation")
        }

    return results
```

### Early Exit Conditions

```python
def loop_with_early_exit(task: str, min_score: int = 80) -> str:
    """Exit early if score exceeds threshold."""
    result = initial_generate(task)

    for i in range(MAX_ITERATIONS):
        score, feedback = evaluate_with_score(result, task)

        if score >= min_score:
            return result

        if i > 0 and score < previous_score:
            # Regression detected, use previous result
            return previous_result

        previous_score = score
        previous_result = result
        result = refine(result, feedback)

    return result
```

---

## Performance Considerations

| Factor | Impact | Optimization |
|--------|--------|--------------|
| Iteration count | 2N+ API calls | Set reasonable max iterations |
| Feedback quality | Determines improvement rate | Specific, actionable evaluator prompts |
| Memory accumulation | Context length growth | Summarize previous attempts |
| Model selection | Cost vs quality | Haiku for evaluation, Sonnet for generation |

---

## Best Practices

1. **Clear Criteria**: Define specific, measurable evaluation criteria
2. **Actionable Feedback**: Evaluator should provide specific improvements
3. **Iteration Limits**: Always set max iterations to prevent infinite loops
4. **Regression Detection**: Monitor if quality decreases between iterations
5. **Chain of Thought**: Maintain full history for debugging and analysis
