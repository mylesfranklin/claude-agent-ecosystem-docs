# Claude Skills System

> **Source**: [Anthropic Cookbook - Skills](https://github.com/anthropics/anthropic-cookbook/tree/main/skills)
> **Purpose**: Specialized capability packages for professional document generation and business automation

## Overview

Skills are **specialized capability packages** enabling Claude to create professional documents, analyze data, and automate business workflows. They feature a **progressive disclosure architecture** for optimized token usage.

## Core Capabilities

| Category | Description |
|----------|-------------|
| **Document Generation** | Excel, PowerPoint, PDF, Word with full formatting |
| **Data Analysis** | Complex spreadsheet operations, pivot tables, visualizations |
| **Business Automation** | Domain-specific workflows with company branding |

---

## Technical Requirements

### Beta Headers Required

```python
headers = {
    "anthropic-beta": ",".join([
        "code-execution-2025-08-25",  # Code execution
        "files-api-2025-04-14",        # File downloads
        "skills-2025-10-02"            # Skills feature
    ])
}
```

### Prerequisites

- Python 3.8+
- Anthropic API key
- Jupyter Notebook environment (for interactive development)

---

## Built-in Skills Reference

| Skill | ID | Purpose | Example Use |
|-------|-----|---------|-------------|
| **Excel** | `xlsx` | Workbooks with formulas, charts, formatting | Financial models, data analysis |
| **PowerPoint** | `pptx` | Professional presentations with transitions | Business decks, reports |
| **PDF** | `pdf` | Formatted documents with tables, images | Contracts, documentation |
| **Word** | `docx` | Rich-text documents with structure | Reports, letters, proposals |

---

## Using Skills

### Invoking a Skill

```python
from anthropic import Anthropic

client = Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=4096,
    betas=["skills-2025-10-02", "files-api-2025-04-14", "code-execution-2025-08-25"],
    messages=[{
        "role": "user",
        "content": "Create an Excel spreadsheet with quarterly sales data and a chart"
    }],
    skills=[{"id": "xlsx"}]
)
```

### Retrieving Generated Files

```python
# Extract file_id from response
file_id = response.content[0].file_id

# Download file
file_content = client.files.content(file_id)

# Save locally
with open("output.xlsx", "wb") as f:
    f.write(file_content.read())
```

---

## File Management API

| Endpoint | Purpose |
|----------|---------|
| `client.files.content(file_id)` | Download file binary content |
| `client.files.retrieve(file_id)` | Get file metadata (name, size) |
| `client.files.list()` | View all stored files with timestamps |
| `client.files.delete(file_id)` | Remove temporary files |

**Note**: Files are stored temporarily on Anthropic's servers and are overwritten when cells rerun.

---

## Custom Skills Development

Custom skills extend Claude's capabilities for domain-specific tasks.

### Directory Structure

```
my-custom-skill/
├── SKILL.md           # Required: Skill instructions
├── scripts/           # Optional: Supporting scripts
│   └── helpers.py
└── resources/         # Optional: Reference materials
    └── templates/
```

### SKILL.md Template

```markdown
# My Custom Skill

## Purpose
[What this skill does and when to use it]

## Inputs Required
- Input 1: Description
- Input 2: Description

## Process
1. Step one
2. Step two
3. Step three

## Output Format
[Describe expected outputs]

## Examples
[Provide example inputs and outputs]
```

---

## Example Custom Skills

### Financial Ratio Calculator

```markdown
# Financial Ratio Calculator

## Purpose
Calculate and analyze key financial ratios from financial statements.

## Inputs
- Balance sheet data (assets, liabilities, equity)
- Income statement data (revenue, expenses, net income)
- Cash flow data (operating, investing, financing)

## Calculated Ratios

### Liquidity Ratios
- Current Ratio = Current Assets / Current Liabilities
- Quick Ratio = (Current Assets - Inventory) / Current Liabilities

### Profitability Ratios
- Gross Margin = (Revenue - COGS) / Revenue
- Net Profit Margin = Net Income / Revenue
- ROE = Net Income / Shareholders' Equity
- ROA = Net Income / Total Assets

### Leverage Ratios
- Debt-to-Equity = Total Debt / Total Equity
- Interest Coverage = EBIT / Interest Expense

## Output
Excel workbook with:
1. Input data sheet
2. Ratio calculations sheet
3. Trend analysis charts
4. Executive summary
```

### Company Brand Guidelines Skill

```markdown
# Brand Guidelines Skill

## Purpose
Apply company-specific branding to all generated documents.

## Brand Elements
- Primary Color: #1a73e8
- Secondary Color: #34a853
- Font Family: Roboto
- Logo placement: Top-left, 120px height

## Document Rules
- Headers use Primary Color
- Accents use Secondary Color
- Footer includes copyright notice
- All documents include company logo
```

---

## Progressive Disclosure Architecture

Skills use **progressive disclosure** to optimize token usage:

1. **Initial Load**: Core skill prompt only
2. **On Demand**: Additional context loaded as needed
3. **Caching**: Frequently used elements cached for efficiency

```
Token Usage Optimization:
┌──────────────────────────────────────┐
│         Full Skill Context           │ ← Loaded only when needed
├──────────────────────────────────────┤
│      Extended Instructions           │ ← Loaded on complex tasks
├──────────────────────────────────────┤
│        Core Skill Prompt             │ ← Always loaded
└──────────────────────────────────────┘
```

---

## Best Practices

### Skill Design

1. **Single Responsibility**: Each skill should do one thing well
2. **Clear Inputs/Outputs**: Document exactly what's needed and produced
3. **Examples**: Include concrete examples in SKILL.md
4. **Error Handling**: Define behavior for edge cases

### Performance

1. **Minimal Context**: Only load what's needed
2. **Efficient Templates**: Pre-structure common outputs
3. **Batch Operations**: Group related tasks together

### Integration

1. **Composability**: Skills should work together
2. **Consistent APIs**: Follow established patterns
3. **Version Control**: Track skill changes over time

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Skill not loading | Verify beta headers are included |
| Empty file output | Check file_id extraction from response |
| Formatting issues | Review SKILL.md instructions |
| Large file failures | Chunk data into smaller requests |
