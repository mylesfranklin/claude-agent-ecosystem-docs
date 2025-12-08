# Writing Effective Tools for Agents

> **Source**: https://www.anthropic.com/engineering/writing-tools-for-agents
> **Category**: Engineering Blog Post

## Core Principles

### 1. Single Responsibility

**Good**:
```json
{
  "name": "read_file",
  "description": "Read contents of a file from the filesystem"
}
```

**Bad**:
```json
{
  "name": "file_operations",
  "description": "Read, write, delete, or list files"
}
```

### 2. Clear Naming

- **Action-oriented**: `search_code`, `create_branch`, `run_tests`
- **Unambiguous**: Avoid `process`, `handle`, `manage`
- **Consistent**: Use same conventions across tools

### 3. Namespacing

Group related tools with prefixes:
```
git_commit
git_push
git_status
file_read
file_write
file_delete
```

## Tool Description Best Practices

### Structure
```json
{
  "name": "tool_name",
  "description": "One sentence explaining what the tool does and when to use it.",
  "parameters": {
    "param1": {
      "type": "string",
      "description": "What this parameter represents"
    }
  }
}
```

### Guidelines

1. **Start with purpose**: "Searches the codebase for..."
2. **Explain when to use**: "Use this when you need to find..."
3. **Note limitations**: "Only searches text files, not binaries"
4. **Provide examples**: "For finding function definitions, use pattern..."

## Token Efficiency

1. **Concise descriptions** - Every word should add value
2. **Structured output** - Return only essential information
3. **Progressive disclosure** - Basic info by default, details on request

## Error Handling

**Good**:
```json
{
  "error": "FILE_NOT_FOUND",
  "message": "File '/src/main.ts' does not exist",
  "suggestion": "Did you mean '/src/main.tsx'?"
}
```

**Bad**:
```json
{
  "error": "Error occurred"
}
```

### Error Categories

| Category | When | Agent Action |
|----------|------|-------------|
| NOT_FOUND | Resource doesn't exist | Search alternatives |
| PERMISSION_DENIED | Access restricted | Request permission |
| INVALID_INPUT | Bad parameters | Fix and retry |
| RATE_LIMITED | Too many requests | Wait and retry |

## Idempotency

Tools should be idempotent where possible:
- Running twice produces same result
- No unintended side effects
- Safe to retry on failure

## Checklist for New Tools

- [ ] Single, clear purpose
- [ ] Descriptive name following conventions
- [ ] Complete parameter documentation
- [ ] Examples provided
- [ ] Error handling specified
- [ ] Idempotent where possible
- [ ] Unit tests written

## Related Resources

- [Building Effective Agents](building-effective-agents.md)
- [Multi-Agent Research System](multi-agent-research.md)
