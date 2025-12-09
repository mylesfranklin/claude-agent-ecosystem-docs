# Document Skills

> **Source**: [anthropics/skills](https://github.com/anthropics/skills)
> **Category**: Production-Grade Document Generation
> **Status**: Official Anthropic Skills

## Overview

Document skills enable Claude to create, edit, and manipulate professional documents with the precision of domain experts. These are **production-grade** implementations used in real-world workflows.

---

## XLSX (Excel/Spreadsheet) Skill

### Purpose
Comprehensive spreadsheet work including creation, editing, analysis, and formula-driven calculations.

### Supported Formats
- `.xlsx` - Excel workbook
- `.xlsm` - Macro-enabled workbook
- `.csv` - Comma-separated values
- `.tsv` - Tab-separated values

### Core Principle

> **"Let Excel calculate."**

Never compute values in Python and embed them. Use formulas so spreadsheets remain dynamic.

```python
# ❌ WRONG - Hardcoded value
cell.value = 150000  # Revenue calculated elsewhere

# ✓ RIGHT - Formula
cell.value = "=SUM(B2:B13)"  # Excel calculates
```

### Financial Model Standards

| Color | Meaning |
|-------|---------|
| **Blue text** | User-changeable inputs |
| **Black text** | Formulas and calculations |
| **Green text** | Internal worksheet links |
| **Red text** | External file links |
| **Yellow background** | Key assumptions requiring attention |

### Zero Error Tolerance

All deliverables must have **no formula errors**:
- `#REF!` - Invalid cell reference
- `#DIV/0!` - Division by zero
- `#VALUE!` - Wrong value type
- `#N/A` - Value not available
- `#NAME?` - Unrecognized name

### Tool Selection

| Library | Use Case |
|---------|----------|
| **pandas** | Data analysis, bulk operations, simple exports |
| **openpyxl** | Complex formatting, formulas, Excel-specific features |

### Verification Checklist

- [ ] Cell references are correct (A1 vs R1C1)
- [ ] Column mappings match Excel notation
- [ ] Row offsets account for 1-indexing
- [ ] Edge cases handled (zero, negative, empty)
- [ ] Formulas recalculated (use `recalc.py`)

### Documentation Standards

Hardcoded values require source citations:

```
Source: [System/Document], [Date], [Specific Reference], [URL]
Example: "Source: Company 10-K, FY2024, Page 45, sec.gov/..."
```

---

## PDF Skill

### Purpose
Comprehensive PDF manipulation including text extraction, table parsing, document creation, merging, splitting, and form handling.

### Capabilities

| Category | Operations |
|----------|------------|
| **Extraction** | Text with layout, tables to DataFrames, images, OCR |
| **Manipulation** | Merge, split, rotate, watermark, encrypt |
| **Creation** | New documents with Canvas, multi-page reports |
| **Metadata** | Read properties, decrypt protected files |

### Tool Stack

| Purpose | Primary Tool |
|---------|--------------|
| Basic operations | `pypdf` |
| Text/table extraction | `pdfplumber` |
| PDF generation | `reportlab` |
| CLI manipulation | `qpdf`, `pdftk`, `pdftotext` |
| OCR | `pytesseract` |

### Text Extraction

```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for page in pdf.pages:
        text = page.extract_text()
        tables = page.extract_tables()
```

### Table to DataFrame

```python
import pandas as pd
import pdfplumber

with pdfplumber.open("report.pdf") as pdf:
    table = pdf.pages[0].extract_table()
    df = pd.DataFrame(table[1:], columns=table[0])
```

### PDF Creation

```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

c = canvas.Canvas("output.pdf", pagesize=letter)
c.drawString(100, 750, "Title")
c.drawString(100, 700, "Content goes here")
c.save()
```

### Merge PDFs

```python
from pypdf import PdfMerger

merger = PdfMerger()
merger.append("doc1.pdf")
merger.append("doc2.pdf")
merger.write("combined.pdf")
merger.close()
```

---

## PPTX (PowerPoint) Skill

### Purpose
Create, edit, and analyze PowerPoint presentations with professional design standards.

### Workflows

#### 1. Reading & Analysis

```bash
# Extract text as markdown
python -m markitdown presentation.pptx

# Access raw XML for advanced features
python ooxml/scripts/unpack.py presentation.pptx output_dir/
```

#### 2. Creating New Presentations

**Without Template (HTML to PPTX):**
1. Design with content-informed approach
2. Create HTML files for each slide
3. Convert using `html2pptx.js`
4. Validate with thumbnail generation

**With Template:**
1. Extract template inventory
2. Analyze available layouts
3. Duplicate/reorder slides
4. Replace text systematically

#### 3. Editing Existing

1. Unpack PPTX to access XML
2. Edit `ppt/slides/slide{N}.xml`
3. Validate changes immediately
4. Repack into final presentation

### Design Principles

> "Consider the subject matter, check for branding, match palette to content."

### Color Palettes

17 pre-designed palettes available:
- Classic (Navy, Burgundy, Forest)
- Modern (Tech, Startup, Clean)
- Thematic (Nature, Ocean, Urban)

### Validation Steps

- [ ] Generate thumbnail grid
- [ ] Check for text cutoff
- [ ] Verify no overlap
- [ ] Confirm positioning
- [ ] Test contrast ratios

---

## DOCX (Word) Skill

### Purpose
Create and edit Word documents with full formatting control and tracked changes support.

### Capabilities

| Operation | Method |
|-----------|--------|
| Text extraction | `pandoc` |
| Creation | `docx-js` (JavaScript) |
| Editing | Python Document library |
| Tracked changes | Systematic redlining workflow |

### Text Extraction

```bash
# With tracked changes preserved
pandoc --track-changes=all document.docx -o output.md
```

### Document Creation (JavaScript)

```javascript
import { Document, Paragraph, TextRun, Packer } from "docx";

const doc = new Document({
  sections: [{
    children: [
      new Paragraph({
        children: [
          new TextRun({ text: "Title", bold: true, size: 48 }),
        ],
      }),
      new Paragraph({
        children: [
          new TextRun("Body text goes here."),
        ],
      }),
    ],
  }],
});

const buffer = await Packer.toBuffer(doc);
```

### Tracked Changes Workflow

1. **Convert to markdown** for planning
2. **Identify batches** (3-10 changes per batch)
3. **Implement systematically** with minimal, precise edits
4. **Preserve unchanged text** - only mark actual changes
5. **Verify completion** before delivery

### Batch Grouping Strategies

| Strategy | When to Use |
|----------|-------------|
| By section | Changes scattered across document |
| By type | Many similar changes (formatting, terminology) |
| By proximity | Clustered changes in same area |

---

## Integration Patterns

### Loading Skills via API

```python
from anthropic import Anthropic

client = Anthropic()

# Single skill
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    betas=["skills-2025-10-02"],
    skills=[{"id": "xlsx"}],
    messages=[...]
)

# Multiple skills
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    betas=["skills-2025-10-02"],
    skills=[
        {"id": "xlsx"},
        {"id": "pdf"}
    ],
    messages=[...]
)
```

### Retrieving Generated Files

```python
# Get file from response
file_id = response.content[0].file_id

# Download
file_content = client.files.content(file_id)

# Save locally
with open("output.xlsx", "wb") as f:
    f.write(file_content.read())
```

---

## Best Practices

### General

1. **Read complete documentation** before starting
2. **Use helper scripts** as black boxes
3. **Validate output** before delivery
4. **Handle edge cases** explicitly

### Performance

1. **Batch operations** when possible
2. **Stream large files** in chunks
3. **Cache repeated operations**
4. **Close resources** after use

### Quality

1. **Zero tolerance for errors**
2. **Preserve formatting** when editing
3. **Test on multiple viewers** (Office, LibreOffice, Preview)
4. **Document data sources**
