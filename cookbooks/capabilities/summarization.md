# Summarization Techniques

> **Source**: [Anthropic Cookbook - Capabilities](https://github.com/anthropics/anthropic-cookbook/tree/main/capabilities)
> **Purpose**: Synthesize information from multiple sources using various approaches

## Overview

Claude excels at summarization through:
- **Multi-shot techniques** for consistency
- **Domain-based methods** for specialized content
- **Chunking strategies** for lengthy documents
- **Multi-document synthesis** for comprehensive overviews

---

## Basic Summarization Patterns

### Simple Summary

```python
def summarize(text: str, client: Anthropic,
              max_length: str = "2-3 paragraphs") -> str:
    """Generate a concise summary."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1000,
        messages=[{
            "role": "user",
            "content": f"""Summarize the following text in {max_length}.
Focus on the key points and main ideas.

Text:
{text}

Summary:"""
        }]
    )

    return response.content[0].text
```

### Extractive vs Abstractive

```python
def extractive_summary(text: str, client: Anthropic,
                       num_sentences: int = 5) -> str:
    """Extract key sentences from the text."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": f"""Extract the {num_sentences} most important sentences
from this text. Quote them exactly as they appear.

Text:
{text}

Key sentences:"""
        }]
    )
    return response.content[0].text


def abstractive_summary(text: str, client: Anthropic) -> str:
    """Generate a new summary in your own words."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": f"""Write a summary of this text in your own words.
Do not quote the original text directly.

Text:
{text}

Summary:"""
        }]
    )
    return response.content[0].text
```

---

## Chunked Summarization

For documents exceeding context limits:

### Map-Reduce Pattern

```python
def map_reduce_summarize(chunks: list[str], client: Anthropic) -> str:
    """Summarize long documents using map-reduce."""
    # Map: Summarize each chunk
    chunk_summaries = []
    for i, chunk in enumerate(chunks):
        summary = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=500,
            messages=[{
                "role": "user",
                "content": f"""Summarize this section (Part {i+1} of {len(chunks)}):

{chunk}

Summary:"""
            }]
        ).content[0].text
        chunk_summaries.append(summary)

    # Reduce: Combine summaries
    combined = "\n\n".join([
        f"Section {i+1}:\n{s}"
        for i, s in enumerate(chunk_summaries)
    ])

    final = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1000,
        messages=[{
            "role": "user",
            "content": f"""Combine these section summaries into a cohesive overall summary:

{combined}

Final Summary:"""
        }]
    ).content[0].text

    return final
```

### Hierarchical Summarization

```python
def hierarchical_summarize(text: str, client: Anthropic,
                           chunk_size: int = 4000) -> dict:
    """Create multi-level summary hierarchy."""
    # Split into chunks
    chunks = [text[i:i+chunk_size] for i in range(0, len(text), chunk_size)]

    # Level 1: Detailed summaries
    detailed_summaries = [
        summarize(chunk, client, "1 paragraph")
        for chunk in chunks
    ]

    # Level 2: Section summary
    section_summary = summarize(
        "\n\n".join(detailed_summaries),
        client,
        "2-3 sentences"
    )

    # Level 3: One-liner
    one_liner = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=100,
        messages=[{
            "role": "user",
            "content": f"Summarize in one sentence:\n\n{section_summary}"
        }]
    ).content[0].text

    return {
        "one_liner": one_liner,
        "executive_summary": section_summary,
        "detailed_summaries": detailed_summaries
    }
```

---

## Multi-Document Summarization

### Synthesizing Multiple Sources

```python
def multi_doc_summarize(documents: list[dict], client: Anthropic) -> str:
    """Synthesize information from multiple documents."""
    docs_text = "\n\n---\n\n".join([
        f"Source: {doc['source']}\nDate: {doc.get('date', 'Unknown')}\n\n{doc['text']}"
        for doc in documents
    ])

    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=2000,
        messages=[{
            "role": "user",
            "content": f"""Synthesize information from these sources into a comprehensive summary.
Identify common themes, note any contradictions, and cite sources.

Documents:
{docs_text}

Synthesized Summary:"""
        }]
    )

    return response.content[0].text
```

### Comparative Summary

```python
def comparative_summary(doc1: str, doc2: str, client: Anthropic) -> str:
    """Compare and contrast two documents."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1500,
        messages=[{
            "role": "user",
            "content": f"""Compare these two documents. Identify:
1. Common points
2. Differences
3. Unique information in each

Document 1:
{doc1}

Document 2:
{doc2}

Comparison:"""
        }]
    )

    return response.content[0].text
```

---

## Domain-Specific Summarization

### Legal Document Summary

```python
LEGAL_TEMPLATE = """
Summarize this legal document with focus on:
1. Parties involved
2. Key obligations and rights
3. Important dates and deadlines
4. Penalties and remedies
5. Governing law and jurisdiction

Document:
{text}

Legal Summary:
"""

def legal_summarize(text: str, client: Anthropic) -> str:
    """Summarize legal documents with key elements."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1500,
        messages=[{
            "role": "user",
            "content": LEGAL_TEMPLATE.format(text=text)
        }]
    )
    return response.content[0].text
```

### Technical Document Summary

```python
TECHNICAL_TEMPLATE = """
Summarize this technical document:

1. Problem/Objective: What problem does this solve?
2. Approach: What method or architecture is used?
3. Key Findings: What are the main results?
4. Limitations: What are the constraints?
5. Applications: Where can this be applied?

Document:
{text}

Technical Summary:
"""

def technical_summarize(text: str, client: Anthropic) -> str:
    """Summarize technical/research documents."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1500,
        messages=[{
            "role": "user",
            "content": TECHNICAL_TEMPLATE.format(text=text)
        }]
    )
    return response.content[0].text
```

### Meeting Notes Summary

```python
MEETING_TEMPLATE = """
Summarize these meeting notes:

1. Attendees: Who was present?
2. Key Decisions: What was decided?
3. Action Items: What needs to be done, by whom, by when?
4. Open Questions: What remains unresolved?
5. Next Steps: What happens next?

Meeting Notes:
{text}

Meeting Summary:
"""

def meeting_summarize(text: str, client: Anthropic) -> str:
    """Summarize meeting notes with actionable format."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1000,
        messages=[{
            "role": "user",
            "content": MEETING_TEMPLATE.format(text=text)
        }]
    )
    return response.content[0].text
```

---

## Structured Output Summaries

### JSON Summary

```python
def json_summarize(text: str, client: Anthropic) -> dict:
    """Generate structured JSON summary."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1000,
        messages=[{
            "role": "user",
            "content": f"""Summarize this text and return as JSON:

{{
    "title": "brief title",
    "main_points": ["point 1", "point 2", ...],
    "key_entities": ["entity 1", "entity 2", ...],
    "sentiment": "positive/negative/neutral",
    "word_count_original": number,
    "summary": "2-3 sentence summary"
}}

Text:
{text}

JSON:"""
        }]
    )

    import json
    return json.loads(response.content[0].text)
```

### Bullet Point Summary

```python
def bullet_summarize(text: str, client: Anthropic,
                     num_bullets: int = 5) -> list[str]:
    """Generate bullet point summary."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": f"""Summarize in exactly {num_bullets} bullet points.
Each bullet should be a complete, standalone point.

Text:
{text}

Bullet points:
•"""
        }]
    )

    bullets = response.content[0].text.split("•")
    return [b.strip() for b in bullets if b.strip()]
```

---

## Quality Improvement Techniques

### Multi-Shot Summarization

```python
def multi_shot_summarize(text: str, client: Anthropic,
                         num_attempts: int = 3) -> str:
    """Generate multiple summaries and select best."""
    summaries = []

    for i in range(num_attempts):
        summary = summarize(text, client)
        summaries.append(summary)

    # Have Claude select the best
    options = "\n\n".join([
        f"Option {i+1}:\n{s}"
        for i, s in enumerate(summaries)
    ])

    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1000,
        messages=[{
            "role": "user",
            "content": f"""Select the best summary and improve it.
Consider accuracy, completeness, and clarity.

{options}

Best improved summary:"""
        }]
    )

    return response.content[0].text
```

---

## Best Practices

| Technique | When to Use | Benefit |
|-----------|-------------|---------|
| Chunked | Long documents | Handles any length |
| Domain-specific | Specialized content | Higher accuracy |
| Multi-shot | Critical summaries | Higher quality |
| Hierarchical | Executive reports | Multiple detail levels |
| Comparative | Multiple sources | Highlights differences |
