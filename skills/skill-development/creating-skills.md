# Creating Custom Skills

> **Source**: [anthropics/skills - skill-creator](https://github.com/anthropics/skills)
> **Purpose**: Build your own skills to extend Claude's capabilities

## Overview

Custom skills let you package domain expertise into reusable modules that Claude can load on demand. This guide covers everything from basic skill structure to production deployment.

---

## Skill Anatomy

### Minimal Skill

```
my-skill/
└── SKILL.md
```

A single file with YAML frontmatter and markdown instructions.

### Complete Skill

```
my-skill/
├── SKILL.md              # Required: Core instructions
├── scripts/              # Optional: Executable code
│   ├── process.py
│   ├── validate.sh
│   └── helpers/
│       └── utils.py
├── resources/            # Optional: Reference documentation
│   ├── api-reference.md
│   ├── examples.md
│   └── troubleshooting.md
└── assets/               # Optional: Templates, media
    ├── template.html
    └── styles.css
```

---

## SKILL.md Structure

### Frontmatter (Required)

```yaml
---
name: my-skill-identifier
description: |
  Clear purpose statement. Include when to use this skill:
  use when analyzing financial data, generating reports,
  or creating budget projections.
---
```

The `description` field is critical - it contains the **triggers** that tell Claude when to activate this skill.

### Body Structure

```markdown
# Skill Title

## When to Use
Explicit triggers and use cases.

## Prerequisites
What must be true before using.

## Process

### Step 1: [Action Verb]
[Detailed instructions]

### Step 2: [Action Verb]
[Detailed instructions]

## Available Tools
Scripts and their purposes.

## Verification
How to confirm success.

## Common Issues
Troubleshooting guidance.
```

---

## Writing Guidelines

### Language Style

Use **imperative/infinitive** form:

```markdown
# ✓ Good
"Extract the data from the PDF"
"Validate all formulas"
"Run the test suite"

# ✗ Bad
"You should extract the data"
"The data needs to be extracted"
"Extracting the data is the first step"
```

### Size Limits

| Component | Limit | Rationale |
|-----------|-------|-----------|
| SKILL.md body | < 500 lines | Context efficiency |
| Metadata | ~100 words | Always loaded |
| Total skill | < 5k words | Practical loading |

### Information Architecture

```
SKILL.md
   │
   ├─► "For formatting details, see resources/formatting.md"
   │
   └─► "For API reference, see resources/api.md"

resources/formatting.md
   │
   └─► [Table of contents for 100+ line files]

DO NOT:
   resources/formatting.md ──► resources/advanced/deep.md
   (No nested references beyond one level)
```

---

## Progressive Disclosure

### Loading Strategy

```
User triggers skill
        │
        ▼
┌───────────────────┐
│ Load: Metadata    │ ~100 words, always loaded
│ (name, description│
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│ Load: SKILL.md    │ <5k words, on activation
│ (instructions)    │
└─────────┬─────────┘
          │ If needed
          ▼
┌───────────────────┐
│ Load: Resources   │ On explicit reference
│ (scripts, docs)   │
└───────────────────┘
```

### Practical Application

```markdown
# In SKILL.md

## Basic Formatting
Apply standard styles:
- Headers: 24pt bold
- Body: 12pt regular
- Lists: bulleted with 16pt spacing

## Advanced Formatting
For complex layouts, conditional formatting, and
custom styles, see `resources/advanced-formatting.md`.
```

---

## Scripts Design

### Black Box Philosophy

Scripts should be **usable without reading source code**:

```bash
# Good: Self-documenting
$ python scripts/process.py --help

Usage: process.py [OPTIONS] INPUT_FILE

Options:
  --format TEXT     Output format (json, csv, xlsx)
  --validate        Run validation checks
  --output PATH     Output file path
  --help           Show this message
```

### Script Guidelines

```python
#!/usr/bin/env python3
"""
Process data files with validation.

Usage:
    python process.py input.csv --format xlsx --validate

Always run with --help first to understand options.
"""

import argparse
import sys

def main():
    parser = argparse.ArgumentParser(
        description='Process data files with validation'
    )
    parser.add_argument('input', help='Input file path')
    parser.add_argument('--format', default='json',
                       choices=['json', 'csv', 'xlsx'])
    parser.add_argument('--validate', action='store_true')
    parser.add_argument('--output', help='Output path')

    args = parser.parse_args()

    # Implementation
    ...

if __name__ == '__main__':
    main()
```

### Error Messages

```python
# ✓ Good: Actionable
raise ValueError(
    f"Invalid date format '{value}'. "
    f"Expected YYYY-MM-DD. Example: 2024-01-15"
)

# ✗ Bad: Unhelpful
raise ValueError("Invalid date")
```

---

## Resources Design

### Reference Document Structure

```markdown
# API Reference

## Table of Contents
- [Authentication](#authentication)
- [Endpoints](#endpoints)
- [Error Handling](#error-handling)

## Authentication
[Content...]

## Endpoints
[Content...]

## Error Handling
[Content...]
```

### When to Split

| Content Length | Action |
|----------------|--------|
| < 100 lines | Include in SKILL.md |
| 100-300 lines | Separate file, add TOC |
| > 300 lines | Split into multiple files |

---

## Development Workflow

### 1. Understand Through Examples

```bash
# Study existing skills
ls skills/
cat skills/xlsx/SKILL.md
cat skills/mcp-builder/SKILL.md
```

### 2. Plan Resources

```markdown
## My Skill Planning

### Core Instructions (SKILL.md)
- When to use triggers
- Main workflow steps
- Verification criteria

### Scripts Needed
- [ ] init.py - Setup workspace
- [ ] process.py - Main processing
- [ ] validate.py - Check outputs

### References Needed
- [ ] api-reference.md - External API docs
- [ ] examples.md - Usage examples
```

### 3. Initialize

```bash
# Using the skill creator
python scripts/init_skill.py my-skill

# Or manually
mkdir -p my-skill/{scripts,resources,assets}
touch my-skill/SKILL.md
```

### 4. Develop Iteratively

```
Write SKILL.md draft
        │
        ▼
Test with Claude ──► Issues? ──► Refine instructions
        │                              │
        ▼                              │
   Works well? ◄───────────────────────┘
        │
        ▼
Add scripts as needed
        │
        ▼
Add resources as needed
        │
        ▼
Package and deploy
```

### 5. Package

```bash
# Using the skill creator
python scripts/package_skill.py my-skill

# Creates distributable package
```

---

## Quality Checklist

### SKILL.md Quality

- [ ] Name follows `kebab-case` convention
- [ ] Description includes "when to use" triggers
- [ ] Process uses imperative verbs
- [ ] Steps are clear and actionable
- [ ] Verification criteria are specific
- [ ] Under 500 lines

### Scripts Quality

- [ ] All have `--help` documentation
- [ ] Error messages are actionable
- [ ] Exit codes are meaningful
- [ ] Dependencies are documented
- [ ] Tested on clean environment

### Resources Quality

- [ ] Files over 100 lines have TOC
- [ ] No nested references beyond one level
- [ ] Examples are tested and working
- [ ] API docs match current version

---

## Common Patterns

### Gate Pattern

```markdown
### Step 1: Gather Requirements

1. Identify all input sources
2. Validate data formats
3. Confirm access permissions

**GATE: Do not proceed until:**
- [ ] All sources identified
- [ ] Formats validated
- [ ] Access confirmed
```

### Branch Pattern

```markdown
### Step 3: Process Data

**If** data is structured (CSV, JSON):
  → Use `scripts/structured.py`

**If** data is unstructured (PDF, images):
  → Use `scripts/extract.py` first
  → Then `scripts/structured.py`
```

### Loop Pattern

```markdown
### Step 4: Iterate Until Quality

**LOOP** until all checks pass:
1. Run validation: `python scripts/validate.py`
2. Review errors
3. Fix issues
4. Re-run validation
5. **If** all pass → Exit loop
```

---

## Testing Your Skill

### Manual Testing

1. Load skill in Claude Code
2. Present test scenarios
3. Verify Claude follows instructions
4. Check output quality
5. Document edge cases

### Subagent Testing

```markdown
## Test: my-skill

### Scenario 1: Basic Usage
**Input**: Standard input file
**Expected**: Correct output format
**Verification**: Compare to reference

### Scenario 2: Edge Case
**Input**: Empty input file
**Expected**: Graceful error message
**Verification**: Error contains guidance
```

---

## Deployment

### Claude Code

```bash
# Install from marketplace
/plugin install my-skill@my-marketplace

# Or local installation
/plugin install ./my-skill
```

### Claude.ai

Upload skill package directly through the interface.

### API

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    betas=["skills-2025-10-02"],
    skills=[{"id": "my-skill"}],  # Your skill ID
    messages=[...]
)
```

---

## Example: Custom Skill

### Financial Analysis Skill

```markdown
---
name: financial-analysis
description: |
  Analyze financial statements and create investment recommendations.
  Use when reviewing company financials, calculating ratios, or
  preparing investment memos.
---

# Financial Analysis

## When to Use
- Analyzing quarterly/annual financial statements
- Calculating financial ratios
- Comparing companies in same industry
- Preparing investment recommendations

## Prerequisites
- Financial statement data (10-K, 10-Q, or similar)
- Industry benchmark data (if comparing)

## Process

### Step 1: Extract Key Metrics
1. Identify revenue, expenses, assets, liabilities
2. Extract from source documents
3. Normalize to standard format

### Step 2: Calculate Ratios
Run: `python scripts/ratios.py input.xlsx`

Key ratios calculated:
- Liquidity: Current, Quick
- Profitability: Gross Margin, ROE, ROA
- Leverage: Debt/Equity, Interest Coverage

### Step 3: Generate Analysis
1. Compare to industry benchmarks
2. Identify trends (3-year minimum)
3. Flag anomalies and risks
4. Formulate recommendation

## Verification
- [ ] All ratios calculated correctly
- [ ] Trends identified with supporting data
- [ ] Recommendation aligns with analysis
- [ ] Sources cited for all data points
```
