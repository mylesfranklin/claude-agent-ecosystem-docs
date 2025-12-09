# Retrieval Augmented Generation (RAG)

> **Source**: [Anthropic Cookbook - Capabilities](https://github.com/anthropics/anthropic-cookbook/tree/main/capabilities)
> **Purpose**: Enable Claude to work with domain-specific knowledge through intelligent retrieval

## Overview

RAG enables Claude to answer questions using **external knowledge bases**, overcoming the limitations of training data and providing up-to-date, domain-specific responses.

## Core Architecture

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    Query     │────▶│   Retriever  │────▶│   Context    │
└──────────────┘     └──────────────┘     └──────────────┘
                                                  │
                                                  ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Response   │◀────│    Claude    │◀────│   Augmented  │
└──────────────┘     └──────────────┘     │    Prompt    │
                                          └──────────────┘
```

---

## Basic RAG Implementation

### 1. Document Chunking

```python
def chunk_document(text: str, chunk_size: int = 1000,
                   overlap: int = 200) -> list[str]:
    """Split document into overlapping chunks."""
    chunks = []
    start = 0

    while start < len(text):
        end = start + chunk_size
        chunk = text[start:end]

        # Find natural break point
        if end < len(text):
            last_period = chunk.rfind('.')
            if last_period > chunk_size // 2:
                end = start + last_period + 1
                chunk = text[start:end]

        chunks.append(chunk.strip())
        start = end - overlap

    return chunks
```

### 2. Embedding Generation

```python
import voyageai

def generate_embeddings(texts: list[str]) -> list[list[float]]:
    """Generate embeddings for text chunks."""
    client = voyageai.Client()

    embeddings = client.embed(
        texts=texts,
        model="voyage-3",
        input_type="document"
    ).embeddings

    return embeddings
```

### 3. Vector Storage

```python
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

class SimpleVectorStore:
    def __init__(self):
        self.documents = []
        self.embeddings = []

    def add(self, documents: list[str], embeddings: list[list[float]]):
        self.documents.extend(documents)
        self.embeddings.extend(embeddings)

    def search(self, query_embedding: list[float], top_k: int = 5) -> list[str]:
        similarities = cosine_similarity(
            [query_embedding],
            self.embeddings
        )[0]

        top_indices = np.argsort(similarities)[-top_k:][::-1]
        return [self.documents[i] for i in top_indices]
```

### 4. RAG Query

```python
def rag_query(query: str, vector_store: SimpleVectorStore,
              client: Anthropic) -> str:
    """Answer query using RAG."""
    # Generate query embedding
    query_embedding = generate_embeddings([query])[0]

    # Retrieve relevant documents
    relevant_docs = vector_store.search(query_embedding, top_k=5)

    # Construct augmented prompt
    context = "\n\n---\n\n".join(relevant_docs)

    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=2048,
        messages=[{
            "role": "user",
            "content": f"""Use the following context to answer the question.
If the answer is not in the context, say so.

Context:
{context}

Question: {query}

Answer:"""
        }]
    )

    return response.content[0].text
```

---

## Advanced Techniques

### Contextual Embeddings

Standard embeddings lose context when chunks are isolated. Contextual embeddings solve this by prepending context.

```python
def create_contextual_embedding(chunk: str, document: str,
                                client: Anthropic) -> str:
    """Add context to chunk before embedding."""
    prompt = f"""<document>
{document}
</document>

Here is the chunk we want to situate:
<chunk>
{chunk}
</chunk>

Please provide a short succinct context to situate this chunk
within the overall document. Answer only with the context."""

    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=200,
        messages=[{"role": "user", "content": prompt}]
    )

    context = response.content[0].text
    return f"{context}\n\n{chunk}"
```

### Hybrid Search (BM25 + Semantic)

Combine keyword and semantic search for better results.

```python
from rank_bm25 import BM25Okapi

class HybridRetriever:
    def __init__(self, documents: list[str], embeddings: list[list[float]]):
        self.documents = documents
        self.embeddings = embeddings

        # BM25 index
        tokenized = [doc.lower().split() for doc in documents]
        self.bm25 = BM25Okapi(tokenized)

    def search(self, query: str, query_embedding: list[float],
               top_k: int = 5, semantic_weight: float = 0.5) -> list[str]:
        # BM25 scores
        bm25_scores = self.bm25.get_scores(query.lower().split())
        bm25_scores = bm25_scores / (bm25_scores.max() + 1e-6)

        # Semantic scores
        semantic_scores = cosine_similarity([query_embedding], self.embeddings)[0]

        # Combine scores
        combined = (semantic_weight * semantic_scores +
                   (1 - semantic_weight) * bm25_scores)

        top_indices = np.argsort(combined)[-top_k:][::-1]
        return [self.documents[i] for i in top_indices]
```

### Re-ranking

Use Claude to re-rank retrieved documents by relevance.

```python
def rerank_documents(query: str, documents: list[str],
                     client: Anthropic, top_k: int = 3) -> list[str]:
    """Re-rank documents using Claude."""
    docs_text = "\n\n".join([
        f"Document {i+1}:\n{doc}"
        for i, doc in enumerate(documents)
    ])

    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": f"""Given the query and documents below, rank the documents
by relevance to the query. Return only the document numbers in order of relevance.

Query: {query}

{docs_text}

Most relevant to least relevant (document numbers only):"""
        }]
    )

    # Parse ranking and return reordered documents
    ranking = parse_ranking(response.content[0].text)
    return [documents[i-1] for i in ranking[:top_k]]
```

---

## Summary Indexing

For large documents, create hierarchical summaries.

```python
def create_summary_index(chunks: list[str], client: Anthropic) -> dict:
    """Create hierarchical summary index."""
    summaries = []

    for chunk in chunks:
        response = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=200,
            messages=[{
                "role": "user",
                "content": f"Summarize this in 2-3 sentences:\n\n{chunk}"
            }]
        )
        summaries.append(response.content[0].text)

    # Create document-level summary
    all_summaries = "\n\n".join(summaries)
    doc_summary = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": f"Create an executive summary:\n\n{all_summaries}"
        }]
    ).content[0].text

    return {
        "document_summary": doc_summary,
        "chunk_summaries": summaries,
        "chunks": chunks
    }
```

---

## Evaluation Metrics

| Metric | Description | Target |
|--------|-------------|--------|
| **Recall@K** | % of relevant docs in top K | > 80% |
| **Precision@K** | % of top K that are relevant | > 70% |
| **MRR** | Mean Reciprocal Rank | > 0.8 |
| **Answer Accuracy** | Correct answers from RAG | > 90% |

---

## Best Practices

### Chunking Strategy

| Document Type | Chunk Size | Overlap |
|---------------|------------|---------|
| Technical docs | 500-800 | 100 |
| Legal documents | 800-1200 | 200 |
| Conversational | 300-500 | 50 |
| Code | By function/class | Context-aware |

### Retrieval Optimization

1. **Query Expansion**: Rephrase queries for better matching
2. **Metadata Filtering**: Pre-filter by date, source, category
3. **Multi-Query**: Generate multiple query variants
4. **Chunk Headers**: Include section titles in chunks

### Production Considerations

1. **Caching**: Cache embeddings and frequent queries
2. **Batch Processing**: Embed documents in batches
3. **Index Updates**: Implement incremental indexing
4. **Monitoring**: Track retrieval quality metrics
