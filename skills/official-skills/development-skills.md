# Development Skills

> **Source**: [anthropics/skills](https://github.com/anthropics/skills)
> **Category**: Development & Technical Skills
> **Status**: Official Anthropic Skills

## Overview

Development skills enable Claude to build, test, and integrate technical systems. These skills transform Claude into a capable development partner for software engineering workflows.

---

## MCP Builder Skill

### Purpose
Build high-quality MCP (Model Context Protocol) servers that enable LLMs to interact with external services through well-designed tools.

### Why MCP Matters

MCP is the **universal connector** between Claude and external systems:

```
┌────────────┐     ┌────────────┐     ┌────────────┐
│   Claude   │◄───►│ MCP Server │◄───►│  External  │
│   (Agent)  │     │   (Tools)  │     │  Service   │
└────────────┘     └────────────┘     └────────────┘

Examples:
• GitHub API → MCP Server → Claude creates issues
• Database → MCP Server → Claude queries data
• Slack → MCP Server → Claude sends messages
```

### Four-Phase Development Process

#### Phase 1: Research & Planning

```markdown
## Planning Checklist

1. API Design
   - Balance comprehensive coverage with specialized workflows
   - Identify high-value integration points

2. Tool Naming Convention
   - Action-oriented: `github_create_issue`
   - Consistent prefixes: `slack_send_message`
   - Clear purpose in name

3. Protocol Study
   - Review: modelcontextprotocol.io
   - Understand tool schemas

4. Stack Selection
   - TypeScript (recommended): Superior SDK, better AI code generation
   - Python: Alternative for Python-centric workflows

5. API Analysis
   - Authentication requirements
   - Rate limits
   - Data formats
```

#### Phase 2: Implementation

**TypeScript Project Structure:**
```
mcp-server/
├── src/
│   ├── index.ts          # Entry point
│   ├── client.ts         # API client
│   ├── tools/            # Tool implementations
│   │   ├── create.ts
│   │   ├── read.ts
│   │   └── update.ts
│   └── types.ts          # Type definitions
├── package.json
└── tsconfig.json
```

**Tool Schema (Zod):**
```typescript
import { z } from "zod";

const CreateIssueSchema = z.object({
  title: z.string().describe("Issue title"),
  body: z.string().optional().describe("Issue body in markdown"),
  labels: z.array(z.string()).optional().describe("Labels to apply"),
});
```

**Tool Annotations:**
```typescript
{
  name: "github_create_issue",
  description: "Create a new GitHub issue",
  annotations: {
    readOnly: false,      // Modifies state
    destructive: false,   // Not destructive
    idempotent: false,    // Creates new each time
    openWorld: true       // Interacts with external service
  },
  inputSchema: CreateIssueSchema
}
```

#### Phase 3: Review & Testing

```bash
# Build and verify
npm run build

# Test with MCP Inspector
npx @anthropic/mcp-inspector

# Type check
npx tsc --noEmit
```

**Quality Checklist:**
- [ ] No code duplication
- [ ] Consistent error handling
- [ ] Actionable error messages
- [ ] Full type coverage
- [ ] Focused data returns (don't overwhelm agent)

#### Phase 4: Evaluation

Create 10 complex, realistic test questions:

```xml
<evaluation>
  <question id="1">
    <text>Find all open issues labeled "bug" in repo X</text>
    <answer>Expected list of issues with IDs and titles</answer>
    <verification>Manual check against GitHub UI</verification>
  </question>
</evaluation>
```

**Requirements:**
- Independent (no question depends on another)
- Read-only (safe to run repeatedly)
- Verifiable (can manually confirm answer)

### Design Principles

| Principle | Implementation |
|-----------|----------------|
| Actionable errors | Guide user toward solutions |
| Focused returns | Return only relevant data |
| Structured output | Use schemas for parsing |
| Clear descriptions | Explain tool purpose and parameters |

### Transport Selection

| Transport | Use Case |
|-----------|----------|
| **Streamable HTTP** | Remote servers, web deployment |
| **stdio** | Local development, CLI integration |

---

## Web App Testing Skill

### Purpose
Python-based testing of local web applications using Playwright for frontend verification, UI debugging, and automated testing.

### Decision Framework

```
                    ┌─────────────────┐
                    │  What type of   │
                    │   application?  │
                    └────────┬────────┘
                             │
              ┌──────────────┴──────────────┐
              │                             │
              ▼                             ▼
       ┌────────────┐                ┌────────────┐
       │  Static    │                │  Dynamic   │
       │   HTML     │                │  Web App   │
       └─────┬──────┘                └─────┬──────┘
             │                             │
             ▼                             ▼
       Read file                    Is server running?
       directly                            │
             │                    ┌────────┴────────┐
             ▼                    │                 │
       Identify                   No               Yes
       selectors                  │                 │
             │                    ▼                 ▼
             ▼               Use helper        Execute
       Write Playwright      with_server       reconnaissance
       script                     │                 │
                                  └────────┬────────┘
                                           │
                                           ▼
                                   Identify selectors
                                           │
                                           ▼
                                   Perform actions
```

### Critical Rule

> **"Always launch chromium in headless mode"**

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True)
    page = browser.new_page()

    # CRITICAL: Wait for network before inspecting DOM
    page.goto("http://localhost:3000")
    page.wait_for_load_state('networkidle')

    # Now safe to inspect
    content = page.content()
    browser.close()
```

### Helper Script Usage

```bash
# Always check help first
python scripts/with_server.py --help

# Use as black box
python scripts/with_server.py "npm start" --port 3000
```

### Reconnaissance Pattern

1. **Screenshot** the rendered page
2. **Inspect** the DOM after networkidle
3. **Discover** selectors from results
4. **Execute** targeted actions

### Selector Best Practices

| Type | Example | Priority |
|------|---------|----------|
| Role | `page.get_by_role("button", name="Submit")` | 1st |
| Text | `page.get_by_text("Login")` | 2nd |
| Test ID | `page.get_by_test_id("submit-btn")` | 3rd |
| CSS | `page.locator(".btn-primary")` | 4th |

### Common Pitfall

```python
# ❌ WRONG - Inspecting before ready
page.goto("http://localhost:3000")
elements = page.locator("div").all()  # Incomplete!

# ✓ RIGHT - Wait for ready
page.goto("http://localhost:3000")
page.wait_for_load_state('networkidle')
elements = page.locator("div").all()  # Complete
```

---

## Web Artifacts Builder Skill

### Purpose
Build sophisticated multi-component React applications for claude.ai artifacts using modern frontend technologies.

### Tech Stack

```
React 18 + TypeScript + Vite
     │
     ├── Tailwind CSS 3.4.1
     ├── shadcn/ui (40+ components)
     ├── Radix UI primitives
     └── Parcel (bundling)
```

### Workflow

```bash
# 1. Initialize project
./scripts/init-artifact.sh my-app

# 2. Develop
# Edit generated files in my-app/

# 3. Bundle
./scripts/bundle-artifact.sh my-app

# 4. Present
# Share bundled HTML file
```

### Pre-Configured Features

- React with TypeScript via Vite
- Tailwind CSS integrated with shadcn/ui theming
- Path aliases (`@/` notation)
- 40+ pre-installed shadcn/ui components
- Complete Radix UI dependencies
- Node 18+ compatibility

### Design Anti-Patterns to Avoid

> **"AI Slop"** - Recognizable low-effort AI design

| Avoid | Alternative |
|-------|-------------|
| Everything centered | Asymmetric, purposeful layouts |
| Purple gradients everywhere | Intentional color choices |
| Uniform rounded corners | Varied, contextual rounding |
| Inter typeface default | Appropriate typography selection |

### Bundling Output

The bundler creates a **single HTML file** containing:
- All JavaScript inlined
- All CSS inlined
- All dependencies resolved

This standalone file can be shared directly in conversations.

---

## Skill Creator (Meta-Skill)

### Purpose
Guide development of new skills that extend Claude's capabilities through specialized knowledge, workflows, and tool integrations.

### Skill Creation Workflow

```
1. UNDERSTAND     2. PLAN          3. INITIALIZE
   Examples ──►   Resources ──►    init_skill.py
                                        │
                                        ▼
   6. ITERATE     5. PACKAGE       4. EDIT
   Real Usage ◄── package_skill ◄── Resources
```

### What to Include

| Include | Exclude |
|---------|---------|
| SKILL.md (required) | README.md |
| Essential scripts | CHANGELOG |
| Necessary references | Auxiliary docs |
| Required assets | Optional examples |

### Writing Guidelines

```markdown
# Good SKILL.md Structure

---
name: my-skill
description: Clear purpose AND when to use this skill
---

## Process

### Step 1: [Imperative verb]
[Specific instructions]

### Step 2: [Imperative verb]
[Specific instructions]
```

### Size Guidelines

| Component | Limit |
|-----------|-------|
| SKILL.md body | < 500 lines |
| Description | Includes "when to use" triggers |
| References | TOC for files > 100 lines |
| Link depth | One level from SKILL.md |

### Progressive Disclosure in Practice

```
SKILL.md:
"For complex formatting, see resources/formatting.md"

NOT:
"For complex formatting, see resources/formatting.md
 which links to resources/advanced/deep-formatting.md"
```

---

## Integration Patterns

### Claude Code Installation

```bash
# Browse available skills
/plugin marketplace browse

# Install development skills
/plugin install mcp-builder@anthropic-agent-skills
/plugin install webapp-testing@anthropic-agent-skills
```

### Combining Skills

```python
# MCP server + testing in one workflow
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    betas=["skills-2025-10-02"],
    skills=[
        {"id": "mcp-builder"},
        {"id": "webapp-testing"}
    ],
    messages=[{
        "role": "user",
        "content": "Build an MCP server for Notion API and write tests for it"
    }]
)
```

---

## Best Practices

### General Development

1. **Read help first** - `--help` before reading source
2. **Use scripts as black boxes** - Don't modify unless necessary
3. **Test incrementally** - Verify each step
4. **Document decisions** - Explain non-obvious choices

### MCP Servers

1. **Actionable errors** - Tell user how to fix
2. **Focused returns** - Don't overwhelm agent
3. **Type everything** - Full type coverage
4. **Test with Inspector** - Verify before deployment

### Web Testing

1. **Wait for networkidle** - Before DOM inspection
2. **Prefer semantic selectors** - Role, text over CSS
3. **Headless always** - No UI dependencies
4. **Close browsers** - Clean up resources
