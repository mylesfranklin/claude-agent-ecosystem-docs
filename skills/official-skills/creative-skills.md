# Creative Skills

> **Source**: [anthropics/skills](https://github.com/anthropics/skills)
> **Category**: Creative & Design Skills
> **Status**: Official Anthropic Skills

## Overview

Creative skills enable Claude to produce sophisticated visual and design artifacts. These skills demonstrate Claude's capacity for aesthetic judgment and generative creativity.

---

## Algorithmic Art Skill

### Purpose
Create interactive generative art using p5.js with algorithmic philosophy foundations.

### Two-Phase Process

```
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 1: ALGORITHMIC PHILOSOPHY                                │
│                                                                 │
│  • Computational aesthetic movement (4-6 paragraphs)           │
│  • Mathematical relationships driving emergence                 │
│  • Noise and randomness patterns                               │
│  • Particle/field dynamics                                      │
│  • Temporal evolution                                           │
│  • Parametric variation                                         │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 2: INTERACTIVE HTML ARTIFACT                             │
│                                                                 │
│  • Self-contained HTML file                                     │
│  • p5.js algorithm (CDN, no external files)                    │
│  • Seeded randomness (reproducible)                            │
│  • Interactive controls (sliders, pickers)                     │
│  • Seed navigation (prev/next/random/jump)                     │
│  • Anthropic branding (Poppins/Lora fonts)                     │
└─────────────────────────────────────────────────────────────────┘
```

### Guiding Principle

> "The final algorithm should appear as though it took countless hours to develop, was refined with care, and comes from someone at the absolute top of their field."

### Seeded Randomness Pattern

```javascript
function setup() {
  createCanvas(800, 600);
  randomSeed(seed);
  noiseSeed(seed);
}

function draw() {
  // All random() and noise() calls now reproducible
  let x = random(width);
  let y = noise(frameCount * 0.01) * height;
}
```

### Parameter Design

Define tunable aspects of the system:

| Parameter Type | Examples |
|----------------|----------|
| Quantities | Number of particles, iterations |
| Scales | Size, spread, amplitude |
| Probabilities | Spawn chance, direction bias |
| Ratios | Aspect, distribution |
| Angles | Rotation, direction |
| Thresholds | Trigger points, limits |

**Not pattern types** - Let the algorithm define patterns, not user selection.

### Template Structure

**Fixed Elements (keep unchanged):**
- Layout structure and sidebar
- Anthropic color scheme
- Seed control section
- Action buttons (regenerate, reset, download PNG)

**Variable Elements (customize):**
- Complete p5.js algorithm
- Parameter definitions
- UI controls for parameters
- Optional color picker

### Philosophy Writing Guide

```markdown
## Philosophy Template

### Movement Name
[Conceptual name for this aesthetic approach]

### Core Computation
How mathematical relationships create emergent beauty.
[Describe the fundamental algorithm in poetic terms]

### Noise & Randomness
How controlled chaos creates organic patterns.
[Explain seeded variation approach]

### Temporal Evolution
How the system breathes and changes.
[Describe animation and time-based behavior]

### Parametric Space
What can be tuned and explored.
[List meaningful parameters]
```

### Design Philosophy

> "Beauty lives in the process, not the final frame."

Each seed generates a unique exploration. The algorithm creates a **space of possibilities**, not a single image.

### Performance Requirements

- Smooth execution
- Optimized for real-time (if animated)
- Visual balance
- Color harmony

---

## Brand Guidelines Skill

### Purpose
Apply organizational brand identity to visual artifacts, ensuring consistency across all generated materials.

### Anthropic Brand Example

#### Color Palette

**Primary Colors:**
```css
--dark: #141413;      /* Text, dark backgrounds */
--light: #faf9f5;     /* Light backgrounds, text on dark */
--mid-gray: #b0aea5;  /* Secondary elements */
--light-gray: #e8e6dc; /* Subtle backgrounds */
```

**Accent Colors:**
```css
--orange: #d97757;    /* Primary accent */
--blue: #6a9bcc;      /* Secondary accent */
--green: #788c5d;     /* Tertiary accent */
```

#### Typography

| Use | Font | Fallback | Size |
|-----|------|----------|------|
| Headings | Poppins | Arial | 24pt+ |
| Body | Lora | Georgia | 12-16pt |

#### Application Rules

```python
from pptx.util import Pt
from pptx.dml.color import RGBColor

# Heading style
heading.font.name = "Poppins"
heading.font.size = Pt(24)
heading.font.color.rgb = RGBColor(0x14, 0x14, 0x13)  # Dark

# Body style
body.font.name = "Lora"
body.font.size = Pt(12)
body.font.color.rgb = RGBColor(0x14, 0x14, 0x13)  # Dark

# Accent shapes cycle through:
accent_colors = ["#d97757", "#6a9bcc", "#788c5d"]
```

### Smart Application

The skill intelligently applies:
- **Font selection** based on element type
- **Color selection** based on background context
- **Accent cycling** for variety
- **Hierarchy preservation** across formatting

### System Fallbacks

No font installation required:
- Poppins → Arial (system)
- Lora → Georgia (system)

---

## Web Artifacts Builder (Creative Context)

### Purpose
Build visually sophisticated web applications that avoid the "AI slop" aesthetic.

### Anti-Pattern Recognition

| AI Slop | Professional Alternative |
|---------|-------------------------|
| Everything centered | Intentional, asymmetric layouts |
| Purple gradients | Purposeful color selection |
| Uniform rounded corners | Contextual border radius |
| Inter everywhere | Typography that serves content |
| Generic illustrations | Meaningful visual elements |

### Design Principles

```markdown
## Before Coding

1. What is the content hierarchy?
2. What emotions should this evoke?
3. What visual language fits the domain?
4. How will users navigate this?
5. What makes this memorable?
```

### Component Selection

40+ shadcn/ui components available:

```typescript
// Import what you need
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardHeader } from "@/components/ui/card"
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs"

// Apply intentional styling
<Button variant="outline" size="lg" className="font-serif">
  Considered Choice
</Button>
```

### Layout Approaches

| Pattern | When to Use |
|---------|-------------|
| Asymmetric grid | Editorial, portfolio |
| Dense information | Dashboards, data apps |
| Generous whitespace | Marketing, minimal |
| Card-based | Collections, browsing |
| Full-bleed sections | Impact, storytelling |

---

## Canvas Design Skill

### Purpose
Create canvas-based visual designs with programmatic precision.

### Use Cases

- Data visualizations
- Generative patterns
- Custom graphics
- Interactive diagrams
- Image manipulation

### Canvas Fundamentals

```javascript
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');

// Drawing primitives
ctx.fillStyle = '#d97757';
ctx.fillRect(0, 0, 100, 100);

ctx.strokeStyle = '#141413';
ctx.lineWidth = 2;
ctx.beginPath();
ctx.arc(200, 200, 50, 0, Math.PI * 2);
ctx.stroke();
```

### Composition Patterns

```javascript
// Layer management
function draw() {
  // 1. Clear
  ctx.clearRect(0, 0, width, height);

  // 2. Background layer
  drawBackground();

  // 3. Content layers (back to front)
  drawGrid();
  drawData();
  drawHighlights();

  // 4. Overlay layer
  drawLabels();
}
```

---

## Frontend Design Skill

### Purpose
Apply UI/UX design principles to frontend development with systematic approaches.

### Design System Thinking

```
┌─────────────────────────────────────────────────────────────────┐
│                      DESIGN TOKENS                              │
│  Colors, typography, spacing, shadows, borders                  │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                      PRIMITIVES                                  │
│  Buttons, inputs, cards, badges                                 │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                      PATTERNS                                    │
│  Forms, navigation, data display, feedback                      │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                      TEMPLATES                                   │
│  Page layouts, application shells                               │
└─────────────────────────────────────────────────────────────────┘
```

### Spacing System

```css
/* 4px base unit */
--space-1: 4px;
--space-2: 8px;
--space-3: 12px;
--space-4: 16px;
--space-6: 24px;
--space-8: 32px;
--space-12: 48px;
--space-16: 64px;
```

### Typography Scale

```css
/* Modular scale: 1.25 ratio */
--text-xs: 0.64rem;   /* 10.24px */
--text-sm: 0.8rem;    /* 12.8px */
--text-base: 1rem;    /* 16px */
--text-lg: 1.25rem;   /* 20px */
--text-xl: 1.563rem;  /* 25px */
--text-2xl: 1.953rem; /* 31.25px */
--text-3xl: 2.441rem; /* 39px */
```

### Color Application

| Context | Background | Text | Border |
|---------|------------|------|--------|
| Default | --bg-primary | --text-primary | --border-default |
| Hover | --bg-secondary | --text-primary | --border-hover |
| Active | --bg-accent | --text-on-accent | --border-accent |
| Disabled | --bg-muted | --text-muted | --border-muted |

---

## Integration Patterns

### Combining Creative Skills

```python
# Generate branded algorithmic art
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    betas=["skills-2025-10-02"],
    skills=[
        {"id": "algorithmic-art"},
        {"id": "brand-guidelines"}
    ],
    messages=[{
        "role": "user",
        "content": "Create generative art using our brand colors for the annual report cover"
    }]
)
```

### Creative Workflow

```
1. BRIEF          2. CONCEPT        3. EXECUTE
   Requirements ──► Philosophy ───► Implementation
                                         │
   5. DELIVER     4. REFINE              │
   Final Asset ◄── Iterate ◄─────────────┘
```

---

## Best Practices

### Aesthetic Quality

1. **Intentionality** - Every design choice has a reason
2. **Consistency** - Follow established patterns
3. **Hierarchy** - Guide the eye through information
4. **Restraint** - Less is more; remove unnecessary elements

### Technical Quality

1. **Performance** - Smooth, responsive interactions
2. **Accessibility** - Consider all users
3. **Responsiveness** - Work across devices
4. **Maintainability** - Clean, documented code

### Process Quality

1. **Concept first** - Philosophy before pixels
2. **Iterate** - Refine through versions
3. **Validate** - Test with real content
4. **Document** - Explain design decisions
