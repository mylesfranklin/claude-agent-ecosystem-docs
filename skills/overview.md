# Anthropic Skills Framework

> **Source**: [github.com/anthropics/skills](https://github.com/anthropics/skills)
> **Status**: Official Anthropic Framework
> **Purpose**: Extend Claude's capabilities through specialized, reusable skill packages

## What Are Skills?

Skills are **modular packages** that teach Claude repeatable approaches for specific domains. They consist of:
- Instructions Claude follows
- Scripts for complex operations
- Reference documentation
- Assets and templates

Skills transform Claude from a general-purpose assistant into a **domain expert** for specific tasks.

```
┌─────────────────────────────────────────────────────────────────┐
│                         SKILL PACKAGE                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   SKILL.md   │  │   scripts/   │  │  resources/  │          │
│  │ Instructions │  │  Executable  │  │  References  │          │
│  │  & Triggers  │  │     Code     │  │   & Assets   │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    CLAUDE WITH CAPABILITIES                      │
│                                                                 │
│  • Document generation (DOCX, PPTX, PDF, XLSX)                  │
│  • MCP server development                                       │
│  • Web application testing                                      │
│  • Brand-compliant designs                                      │
│  • Generative art creation                                      │
│  • Custom domain expertise                                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Core Design Principles

### 1. Concise Context Usage

> "The context window is a public good."

Skills assume Claude is already capable. They only add information Claude genuinely needs - not tutorials or background knowledge.

### 2. Progressive Disclosure

```
Loading Strategy:
┌────────────────────────┐
│  Metadata (~100 words) │ ← Always loaded
├────────────────────────┤
│   SKILL.md (<5k words) │ ← Loaded when triggered
├────────────────────────┤
│  Bundled Resources     │ ← Loaded as needed
└────────────────────────┘
```

### 3. Appropriate Specificity

Match constraint level to task fragility:

| Task Type | Constraint Level | Format |
|-----------|------------------|--------|
| Flexible approaches | Light | Text instructions |
| Patterns with variations | Medium | Pseudocode |
| Error-prone operations | High | Specific scripts |

---

## Skill Structure

### Minimal Skill

```
my-skill/
└── SKILL.md
```

### Complete Skill

```
my-skill/
├── SKILL.md              # Required: Instructions + frontmatter
├── scripts/              # Optional: Executable code
│   ├── helper.py
│   └── process.sh
├── resources/            # Optional: Reference docs
│   ├── api-reference.md
│   └── patterns.md
└── assets/               # Optional: Templates, media
    └── template.html
```

### SKILL.md Format

```markdown
---
name: skill-identifier
description: Clear functional purpose and when to use this skill
---

# Skill Title

## When to Use
[Triggers that activate this skill]

## Process

### Step 1: [Action]
[Instructions]

### Step 2: [Action]
[Instructions]

## Tools & Scripts
[Available helper scripts]

## Verification
[How to confirm success]
```

---

## Available Skills

### Document Skills (Production-Grade)

| Skill | Purpose | Key Capabilities |
|-------|---------|------------------|
| **xlsx** | Excel/Spreadsheet | Formulas, analysis, financial models |
| **pptx** | PowerPoint | Create, edit, template-based slides |
| **pdf** | PDF Processing | Extract, merge, split, create, OCR |
| **docx** | Word Documents | Create, edit, tracked changes |

### Development Skills

| Skill | Purpose | Key Capabilities |
|-------|---------|------------------|
| **mcp-builder** | MCP Server Dev | Build tools for Claude integration |
| **webapp-testing** | Test Web Apps | Playwright-based UI testing |
| **web-artifacts-builder** | React Apps | Build sophisticated web artifacts |
| **skill-creator** | Create Skills | Meta-skill for building skills |

### Creative Skills

| Skill | Purpose | Key Capabilities |
|-------|---------|------------------|
| **algorithmic-art** | Generative Art | p5.js interactive artwork |
| **brand-guidelines** | Brand Compliance | Apply brand standards |
| **canvas-design** | Design Creation | Canvas-based visual design |
| **frontend-design** | UI/UX Design | Frontend design patterns |

### Enterprise Skills

| Skill | Purpose | Key Capabilities |
|-------|---------|------------------|
| **internal-comms** | Communications | Internal messaging systems |
| **doc-coauthoring** | Collaboration | Document co-creation |
| **theme-factory** | Theming | Create and manage themes |

---

## Integration Paths

### Claude Code

```bash
# Via marketplace
/plugin marketplace browse

# Direct install
/plugin install document-skills@anthropic-agent-skills

# Install specific skill
/plugin install xlsx@anthropic-agent-skills
```

### Claude.ai

- Pre-built skills available to paid subscribers
- Custom skills can be uploaded directly
- Skills activate based on conversation context

### Claude API

```python
from anthropic import Anthropic

client = Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=4096,
    betas=["skills-2025-10-02"],
    skills=[{"id": "xlsx"}],  # Activate skill
    messages=[{
        "role": "user",
        "content": "Create a financial model for quarterly revenue"
    }]
)
```

---

## Key Statistics

- **19.9K+ stars** on GitHub
- **1.9K+ forks**
- **16 official skills**
- Primary languages: Python (83.9%), JavaScript (9.4%)

---

## The AGI Trajectory

Skills represent a paradigm shift:

```
Traditional AI:
    Prompt → Response → Done

Skills-Enabled AI:
    Trigger → Load Expertise → Execute Scripts → Generate Artifacts → Iterate

This creates:
    • Domain-specific capabilities on demand
    • Executable code generation
    • Tool integration via MCP
    • Persistent workflow patterns
```

When combined with:
- **Claude Code** (agent harness)
- **MCP** (external tool integration)
- **Extended Thinking** (deep reasoning)
- **Subagents** (parallel execution)

The result is a system that can autonomously handle complex, multi-step professional workflows - the practical manifestation of general-purpose capability.

---

## Next Steps

- [Document Skills](official-skills/document-skills.md) - XLSX, PPTX, PDF, DOCX
- [Development Skills](official-skills/development-skills.md) - MCP, Testing, Web Apps
- [Creating Skills](skill-development/creating-skills.md) - Build your own
- [Integration Guide](integration/integration-guide.md) - Deploy skills
