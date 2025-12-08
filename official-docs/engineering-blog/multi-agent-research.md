# How We Built Our Multi-Agent Research System

> **Source**: https://www.anthropic.com/engineering/multi-agent-research-system
> **Category**: Engineering Blog Post

## Architecture: Orchestrator-Worker Pattern

### The Orchestrator Agent

**Responsibilities**:
- Receives high-level research tasks
- Decomposes into subtasks
- Assigns work to specialized workers
- Synthesizes results into coherent output

**Key Design Decisions**:
1. Orchestrator doesn't do actual research
2. Maintains high-level view
3. Focuses on coordination and synthesis

### Worker Agents

**Specialized Roles**:
- **Researcher** - Gathers information from sources
- **Analyzer** - Processes and interprets data
- **Writer** - Produces written content
- **Reviewer** - Validates quality and accuracy

## Communication Protocol

### Task Assignment
```json
{
  "task_id": "research_001",
  "type": "research",
  "query": "Find recent papers on transformer architectures",
  "constraints": {
    "time_range": "2023-2024",
    "max_results": 10
  }
}
```

### Result Reporting
```json
{
  "task_id": "research_001",
  "status": "completed",
  "findings": [...],
  "confidence": 0.85,
  "sources": [...]
}
```

## Lessons Learned

### 1. Task Decomposition is Critical
**Problem**: Vague tasks led to inconsistent results
**Solution**: Enforce structured task specifications

### 2. Context Isolation Improves Focus
**Problem**: Workers got distracted by irrelevant information
**Solution**: Provide only task-relevant context

### 3. Explicit Handoff Protocols
**Problem**: Information lost between agent transitions
**Solution**: Standardized result formats

### 4. Progressive Summarization
**Problem**: Context windows overwhelmed
**Solution**: Summarize intermediate results

### 5. Quality Gates
**Problem**: Low-quality work propagated through system
**Solution**: Review checkpoints before synthesis

## Evaluation Metrics

| Metric | Description | Target |
|--------|-------------|--------|
| Task Completion | % tasks successfully completed | >90% |
| Accuracy | Correctness of findings | >85% |
| Efficiency | Tokens per research unit | Minimize |
| Latency | Time to completion | <10min |

## Scaling Considerations

- **Horizontal Scaling**: Workers are stateless, can spawn many in parallel
- **Cost Management**: Track token usage per agent, set budgets
- **Fault Tolerance**: Retry failed tasks, reassign unresponsive workers

## Related Resources

- [Building Effective Agents](building-effective-agents.md)
- [Writing Tools for Agents](writing-tools-for-agents.md)
