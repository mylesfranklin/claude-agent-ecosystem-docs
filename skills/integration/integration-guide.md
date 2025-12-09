# Skills Integration Guide

> **Source**: [anthropics/skills](https://github.com/anthropics/skills)
> **Purpose**: Deploy and integrate skills across Claude platforms

## Overview

Skills can be integrated across multiple platforms:
- **Claude Code** - Terminal-based development
- **Claude.ai** - Web interface
- **Claude API** - Programmatic access

This guide covers integration patterns for each.

---

## Claude Code Integration

### Marketplace Installation

```bash
# Browse available skills
/plugin marketplace browse

# Add a marketplace
/plugin marketplace add anthropic-agent-skills

# Install from marketplace
/plugin install xlsx@anthropic-agent-skills
/plugin install pdf@anthropic-agent-skills
```

### Local Installation

```bash
# Install from local directory
/plugin install ./my-custom-skill

# Install from git repository
/plugin install github:username/my-skill
```

### Skill Activation

Skills activate automatically based on context:

```
User: "Create a quarterly revenue spreadsheet"
      │
      ▼
Claude Code detects: spreadsheet → xlsx skill
      │
      ▼
Skill loaded, Claude follows xlsx instructions
```

### Manual Activation

```bash
# Explicitly request a skill
/skill xlsx

# Or reference in conversation
"Using the xlsx skill, create a budget template"
```

---

## Claude.ai Integration

### Pre-built Skills

Paid subscribers have access to built-in document skills:
- XLSX (Excel)
- PPTX (PowerPoint)
- PDF
- DOCX (Word)

### Custom Skill Upload

1. Package your skill as a directory or ZIP
2. Navigate to Settings → Skills
3. Upload skill package
4. Skill appears in available skills list

### Artifact Integration

Skills that generate files integrate with Claude's artifact system:

```
User Request
      │
      ▼
Skill Execution
      │
      ▼
┌─────────────────┐
│    Artifact     │
│  (downloadable) │
│                 │
│  📄 output.xlsx │
│  [Download]     │
└─────────────────┘
```

---

## API Integration

### Beta Headers Required

```python
from anthropic import Anthropic

client = Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=4096,
    betas=[
        "skills-2025-10-02",      # Skills feature
        "files-api-2025-04-14",   # File downloads
        "code-execution-2025-08-25"  # Code execution
    ],
    skills=[{"id": "xlsx"}],
    messages=[{
        "role": "user",
        "content": "Create a sales forecast spreadsheet"
    }]
)
```

### Single Skill

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    betas=["skills-2025-10-02"],
    skills=[{"id": "pdf"}],
    messages=[{
        "role": "user",
        "content": "Extract tables from this PDF"
    }]
)
```

### Multiple Skills

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    betas=["skills-2025-10-02"],
    skills=[
        {"id": "xlsx"},
        {"id": "pdf"},
        {"id": "brand-guidelines"}
    ],
    messages=[{
        "role": "user",
        "content": "Extract data from PDF and create branded Excel report"
    }]
)
```

### File Retrieval

```python
# Get file ID from response
for content_block in response.content:
    if hasattr(content_block, 'file_id'):
        file_id = content_block.file_id

        # Download file
        file_content = client.files.content(file_id)

        # Save locally
        with open("output.xlsx", "wb") as f:
            f.write(file_content.read())
```

### File Metadata

```python
# Get file information
file_info = client.files.retrieve(file_id)
print(f"Filename: {file_info.filename}")
print(f"Size: {file_info.size} bytes")
print(f"Created: {file_info.created_at}")

# List all files
files = client.files.list()
for f in files:
    print(f"{f.id}: {f.filename}")

# Delete file
client.files.delete(file_id)
```

---

## MCP + Skills Integration

### Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLAUDE                                  │
│                                                                 │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐      │
│  │    Skill     │    │    Skill     │    │    Skill     │      │
│  │    (xlsx)    │    │    (mcp)     │    │   (custom)   │      │
│  └──────┬───────┘    └──────┬───────┘    └──────┬───────┘      │
│         │                   │                   │               │
└─────────┼───────────────────┼───────────────────┼───────────────┘
          │                   │                   │
          ▼                   ▼                   ▼
    ┌──────────┐        ┌──────────┐        ┌──────────┐
    │  Files   │        │   MCP    │        │ External │
    │  System  │        │ Servers  │        │   APIs   │
    └──────────┘        └──────────┘        └──────────┘
```

### Example: Data Pipeline

```python
# Combine MCP tools with document skills
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    betas=["skills-2025-10-02"],
    skills=[
        {"id": "xlsx"},
        {"id": "mcp-builder"}
    ],
    tools=[
        # MCP-provided tools
        {"type": "mcp", "server": "database-server"},
        {"type": "mcp", "server": "api-server"}
    ],
    messages=[{
        "role": "user",
        "content": """
        1. Query sales data from database (last 12 months)
        2. Fetch market data from API
        3. Create Excel report with analysis and charts
        """
    }]
)
```

---

## Skill Composition Patterns

### Sequential Skills

```python
# First skill processes, second skill formats
messages = [
    {"role": "user", "content": "Extract data from report.pdf"},
    # After first response...
    {"role": "user", "content": "Now create Excel summary of extracted data"}
]

# Skills loaded based on each message context
```

### Parallel Skills

```python
# Multiple skills active simultaneously
skills=[
    {"id": "pdf"},      # For input
    {"id": "xlsx"},     # For output
    {"id": "brand-guidelines"}  # For styling
]
```

### Skill Chains

```python
# Workflow with multiple skill invocations
def process_quarterly_report(pdf_path):
    # Step 1: Extract with PDF skill
    extract_response = client.messages.create(
        skills=[{"id": "pdf"}],
        messages=[{
            "role": "user",
            "content": f"Extract all tables from {pdf_path}"
        }]
    )

    # Step 2: Analyze with custom skill
    analysis_response = client.messages.create(
        skills=[{"id": "financial-analysis"}],
        messages=[{
            "role": "user",
            "content": f"Analyze this data: {extract_response.content}"
        }]
    )

    # Step 3: Generate report with xlsx skill
    report_response = client.messages.create(
        skills=[{"id": "xlsx"}, {"id": "brand-guidelines"}],
        messages=[{
            "role": "user",
            "content": f"Create branded report: {analysis_response.content}"
        }]
    )

    return report_response
```

---

## Error Handling

### Skill Not Found

```python
try:
    response = client.messages.create(
        skills=[{"id": "nonexistent-skill"}],
        messages=[...]
    )
except anthropic.BadRequestError as e:
    if "skill not found" in str(e):
        print("Skill not available. Check skill ID.")
```

### File Generation Failure

```python
response = client.messages.create(
    skills=[{"id": "xlsx"}],
    messages=[...]
)

# Check for file in response
file_blocks = [b for b in response.content if hasattr(b, 'file_id')]
if not file_blocks:
    print("No file generated. Check skill execution.")
    print(f"Response: {response.content}")
```

### Beta Feature Errors

```python
try:
    response = client.messages.create(
        betas=["skills-2025-10-02"],
        skills=[{"id": "xlsx"}],
        messages=[...]
    )
except anthropic.BadRequestError as e:
    if "beta" in str(e).lower():
        print("Beta headers may be incorrect or expired")
```

---

## Best Practices

### Performance

| Practice | Benefit |
|----------|---------|
| Load only needed skills | Reduced context usage |
| Batch file operations | Fewer API calls |
| Cache skill outputs | Avoid regeneration |
| Use appropriate model | Balance cost/quality |

### Security

| Practice | Implementation |
|----------|----------------|
| Validate skill sources | Only install trusted skills |
| Review script contents | Check before execution |
| Limit file access | Scope to needed directories |
| Audit skill usage | Log skill invocations |

### Reliability

| Practice | Implementation |
|----------|----------------|
| Handle missing skills | Graceful fallbacks |
| Verify file outputs | Check before using |
| Implement retries | For transient failures |
| Monitor skill health | Track success rates |

---

## Deployment Checklist

### Before Production

- [ ] Test all skill combinations used
- [ ] Verify file outputs are valid
- [ ] Check error handling paths
- [ ] Confirm beta headers are current
- [ ] Review security implications
- [ ] Set up monitoring

### Monitoring Points

```python
import logging

logging.info(f"Skill invoked: {skill_ids}")
logging.info(f"Files generated: {file_count}")
logging.info(f"Response time: {duration}ms")
logging.error(f"Skill error: {error_message}")
```

---

## Quick Reference

### API Pattern

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=4096,
    betas=["skills-2025-10-02", "files-api-2025-04-14"],
    skills=[{"id": "skill-name"}],
    messages=[{"role": "user", "content": "..."}]
)
```

### File Download Pattern

```python
file_id = response.content[0].file_id
content = client.files.content(file_id)
with open("output.ext", "wb") as f:
    f.write(content.read())
```

### Claude Code Pattern

```bash
/plugin install skill-name@marketplace
# or
/skill skill-name
```
