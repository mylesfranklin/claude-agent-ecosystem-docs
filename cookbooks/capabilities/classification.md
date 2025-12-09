# Text Classification with Claude

> **Source**: [Anthropic Cookbook - Capabilities](https://github.com/anthropics/anthropic-cookbook/tree/main/capabilities)
> **Purpose**: Revolutionize classification tasks with complex business rules and limited training data

## Overview

Claude excels at classification tasks, especially when:
- **Complex business rules** govern categorization
- **Limited training data** is available
- **Context understanding** is crucial
- **Multi-label classification** is needed

---

## Basic Classification Pattern

### Simple Prompt-Based Classification

```python
def classify(text: str, categories: list[str], client: Anthropic) -> str:
    """Classify text into one of the given categories."""
    categories_str = ", ".join(categories)

    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=50,
        messages=[{
            "role": "user",
            "content": f"""Classify the following text into exactly one category.
Categories: {categories_str}

Text: {text}

Category:"""
        }]
    )

    return response.content[0].text.strip()
```

### With Confidence Score

```python
def classify_with_confidence(text: str, categories: list[str],
                              client: Anthropic) -> dict:
    """Classify with confidence score."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=100,
        messages=[{
            "role": "user",
            "content": f"""Classify the following text and provide a confidence score.

Categories: {", ".join(categories)}
Text: {text}

Respond in this exact format:
Category: [category]
Confidence: [0-100]
Reasoning: [brief explanation]"""
        }]
    )

    return parse_classification_response(response.content[0].text)
```

---

## Advanced Classification Techniques

### Few-Shot Classification

Provide examples to guide classification:

```python
def few_shot_classify(text: str, examples: list[dict],
                      categories: list[str], client: Anthropic) -> str:
    """Classify using few-shot examples."""
    examples_str = "\n".join([
        f"Text: {ex['text']}\nCategory: {ex['category']}\n"
        for ex in examples
    ])

    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=50,
        messages=[{
            "role": "user",
            "content": f"""Classify texts into categories based on these examples:

{examples_str}

Now classify this text:
Text: {text}
Category:"""
        }]
    )

    return response.content[0].text.strip()
```

### Multi-Label Classification

When items can belong to multiple categories:

```python
def multi_label_classify(text: str, categories: list[str],
                          client: Anthropic) -> list[str]:
    """Classify into multiple applicable categories."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=200,
        messages=[{
            "role": "user",
            "content": f"""Identify ALL applicable categories for this text.
A text can belong to multiple categories or none.

Available categories: {", ".join(categories)}

Text: {text}

List only the applicable categories, one per line.
If none apply, respond with "None".

Applicable categories:"""
        }]
    )

    result = response.content[0].text.strip()
    if result.lower() == "none":
        return []
    return [cat.strip() for cat in result.split("\n") if cat.strip()]
```

### Hierarchical Classification

For nested category structures:

```python
TAXONOMY = {
    "Technology": ["Software", "Hardware", "AI/ML", "Security"],
    "Business": ["Finance", "Marketing", "Operations", "Strategy"],
    "Science": ["Biology", "Physics", "Chemistry", "Medicine"]
}

def hierarchical_classify(text: str, taxonomy: dict,
                          client: Anthropic) -> dict:
    """Two-level hierarchical classification."""
    # First level: main category
    main_categories = list(taxonomy.keys())
    main_category = classify(text, main_categories, client)

    # Second level: subcategory
    if main_category in taxonomy:
        subcategories = taxonomy[main_category]
        subcategory = classify(text, subcategories, client)
    else:
        subcategory = None

    return {
        "main_category": main_category,
        "subcategory": subcategory
    }
```

---

## Classification with Business Rules

### Rule-Enhanced Classification

```python
BUSINESS_RULES = """
Classification Rules:
1. If the text mentions "urgent" or "emergency", always classify as HIGH_PRIORITY
2. Customer complaints about billing go to BILLING even if they mention technical issues
3. Feature requests should be PRODUCT unless they're bug reports in disguise
4. Security-related issues always go to SECURITY regardless of other content
5. Vague inquiries default to GENERAL_SUPPORT
"""

def rule_enhanced_classify(text: str, client: Anthropic) -> dict:
    """Classify with explicit business rules."""
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=300,
        messages=[{
            "role": "user",
            "content": f"""{BUSINESS_RULES}

Classify the following text according to these rules.
Explain which rules you applied.

Text: {text}

Respond in format:
Category: [category]
Applied Rules: [list of rule numbers]
Explanation: [brief explanation]"""
        }]
    )

    return parse_rule_response(response.content[0].text)
```

---

## Batch Classification

### Efficient Multi-Item Classification

```python
def batch_classify(texts: list[str], categories: list[str],
                   client: Anthropic, batch_size: int = 10) -> list[str]:
    """Classify multiple texts efficiently."""
    results = []

    for i in range(0, len(texts), batch_size):
        batch = texts[i:i + batch_size]

        # Format batch
        batch_str = "\n".join([
            f"{j+1}. {text}"
            for j, text in enumerate(batch)
        ])

        response = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=500,
            messages=[{
                "role": "user",
                "content": f"""Classify each text into one category.
Categories: {", ".join(categories)}

Texts:
{batch_str}

Respond with only the number and category for each:
1. [category]
2. [category]
..."""
            }]
        )

        batch_results = parse_batch_response(response.content[0].text)
        results.extend(batch_results)

    return results
```

---

## RAG-Enhanced Classification

Use retrieved examples to improve classification:

```python
def rag_classify(text: str, vector_store: VectorStore,
                 client: Anthropic) -> str:
    """Classification enhanced with similar examples."""
    # Retrieve similar classified examples
    similar = vector_store.search(text, top_k=5)

    examples_str = "\n".join([
        f"Text: {ex['text']}\nCategory: {ex['category']}"
        for ex in similar
    ])

    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=100,
        messages=[{
            "role": "user",
            "content": f"""Here are similar texts and their categories:

{examples_str}

Based on these examples, classify:
Text: {text}
Category:"""
        }]
    )

    return response.content[0].text.strip()
```

---

## Evaluation Framework

### Classification Metrics

```python
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score,
    f1_score, confusion_matrix, classification_report
)

def evaluate_classifier(predictions: list[str],
                        ground_truth: list[str]) -> dict:
    """Evaluate classification performance."""
    return {
        "accuracy": accuracy_score(ground_truth, predictions),
        "precision_macro": precision_score(ground_truth, predictions,
                                           average='macro'),
        "recall_macro": recall_score(ground_truth, predictions,
                                     average='macro'),
        "f1_macro": f1_score(ground_truth, predictions, average='macro'),
        "confusion_matrix": confusion_matrix(ground_truth, predictions),
        "report": classification_report(ground_truth, predictions)
    }
```

### Error Analysis

```python
def analyze_errors(texts: list[str], predictions: list[str],
                   ground_truth: list[str]) -> list[dict]:
    """Analyze misclassifications."""
    errors = []

    for text, pred, truth in zip(texts, predictions, ground_truth):
        if pred != truth:
            errors.append({
                "text": text,
                "predicted": pred,
                "actual": truth,
                "text_length": len(text),
                "contains_keywords": extract_keywords(text)
            })

    return errors
```

---

## Best Practices

### Prompt Design

1. **Be Specific**: List exact categories, don't assume
2. **Provide Context**: Explain what each category means
3. **Handle Edge Cases**: Define behavior for ambiguous cases
4. **Request Format**: Specify exact output format

### Performance Optimization

| Technique | When to Use | Impact |
|-----------|-------------|--------|
| Batching | High volume | 5-10x throughput |
| Caching | Repeated texts | Instant response |
| Smaller model | Simple categories | 3x cost reduction |
| Few-shot | Complex rules | Higher accuracy |

### Quality Assurance

1. **Test Set**: Maintain labeled test set for regression testing
2. **Edge Cases**: Include ambiguous examples in testing
3. **Drift Detection**: Monitor classification distribution over time
4. **Human Review**: Periodically review low-confidence predictions
