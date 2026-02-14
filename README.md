# Symbolic MIR RAG — Architecture Blueprint  
## Domain-Aware Retrieval over Structured Harmonic Systems  

---

## Overview

This repository documents the architecture and engineering design principles behind a domain-aware Retrieval-Augmented Generation (RAG) system built for symbolic Music Information Retrieval (MIR).

The system operates over a structured harmonic knowledge base derived from large-scale modal compatibility modeling.

The production dataset, harmonic engine, and proprietary symbolic computation layers are **not included**.  
This repository presents the architectural blueprint, retrieval mechanics, ranking logic, and constraint strategies behind the system.

---

## Problem

Most RAG systems operate over unstructured text corpora.

This project addresses a different challenge:

**How do we build a retrieval layer over a deterministic symbolic music system — while preserving structural validity, traceability, and explainability?**

The objective is not music generation.

The objective is controlled natural-language interaction with a formally modeled harmonic knowledge base.

---

## Core Design Principle

Structured domain modeling precedes language modeling.

- Harmonic logic remains fully deterministic.
- Retrieval operates over structured, schema-aware objects.
- Strict prompt guardrails are implemented to prevent hallucinated harmonic reasoning.
- The system enforces explicit separation between symbolic computation and probabilistic language generation.

---

## System Architecture

The system follows a layered architecture:

```
Symbolic Harmonic Dataset (structured objects)
        ↓
Schema-aware transformation
        ↓
Chunking strategy (structural + semantic boundaries)
        ↓
Embedding layer (local model)
        ↓
Vector index (FAISS)
        ↓
Retrieval stage (hybrid + structural filtering)
        ↓
Constrained LLM verbalization
```


---

# Layer Breakdown (Engineering View)

---

## 1️⃣ Symbolic Dataset

The knowledge base consists of structured harmonic objects representing:

- Scale identity
- Tonic
- Modal degree
- Interval formulas
- Compatibility mappings
- Quantitative match values
- Modulation identifiers (traceable keys)

All relationships are deterministically generated.

No scraped web data.  
No generative synthesis.  

---

## 2️⃣ Schema-Aware Transformation

Structured symbolic objects are transformed into retrieval-ready representations while preserving:

- Referential identifiers
- Structural boundaries
- Modulation keys
- Chord symbols
- Quantitative match metadata

The transformation layer ensures:

- Traceability from embedding back to symbolic origin
- Schema preservation after vectorization

---

## 3️⃣ Embedding & Indexing

- Local embedding model (MiniLM-based transformer)
- Normalized embeddings
- FAISS vector index (multi-GB scale)
- Row alignment via SQLite metadata store

Each FAISS index entry maps deterministically to a symbolic record.

The LLM does not store knowledge — the index does.

---

## 4️⃣ Hybrid Retrieval Engine

Retrieval is not plain cosine similarity.

It combines:

### Vector Similarity
- Oversampling strategy (`k * N` expansion)
- Normalized cosine similarity
- Base score inversion for ranking

### Structural Filtering
- Query normalization (unicode-safe)
- Multi-language handling (ES ↔ EN normalization)
- Explicit pair extraction (e.g., "between X and Y")
- Literal containment matching
- Domain-specific structural boosts

### Fallback Deterministic Query
If semantic similarity fails to retrieve explicit pair matches:

- Direct SQLite LIKE-based structural query
- Deterministic fallback selection

### Ranking Strategy

Final ranking may incorporate:

- Cosine similarity
- Domain match boosts
- Structural match reinforcement
- Tonic repetition penalty
- Scale-pair repetition penalty
- Optional CrossEncoder reranking
- Maximal Marginal Relevance (MMR) diversification

This prevents:

- Redundant tonic dominance
- Overconcentration of similar harmonic pairs
- Pure embedding bias

Retrieval is hybrid: semantic + symbolic.

---

## 5️⃣ Controlled LLM Layer

The LLM layer is strictly constrained.

### Hard Constraints

- Use exclusively retrieved context
- No theoretical inference beyond literal content
- Only mention chord symbols present in context
- Cite structural identifiers exactly as stored
- Return explicit "not specified" if absent

### Prompt Guardrails

- Extract allowed chord vocabulary dynamically
- Context truncation safeguards
- Strict output format contract (multi-section structured output)
- Controlled generation parameters (temperature, top-p, max tokens, stop tokens)

The LLM does not compute harmonic theory.  
It formats retrieved symbolic relationships into natural language.

---

## 6️⃣ Backend Architecture

The system includes:

- FastAPI backend
- FAISS index loading
- SQLite metadata alignment
- Context retrieval endpoints
- Inspection/debug endpoints
- Structured JSON response formatting
- Local LLM invocation via API (e.g., Mistral via Ollama)

All layers are modular.

Embedding generation, indexing, retrieval, and verbalization are independently replaceable.

---

## Engineering Highlights

- ~5GB FAISS index built from structured symbolic dataset
- SentenceTransformers (MiniLM) embedding pipeline
- Hybrid ranking (vector similarity + domain-specific structural filtering)
- SQLite metadata alignment layer
- Query normalization and multi-language handling
- Explicit pair detection in user queries
- Prompt constraint enforcement (allowed tokens filtering)
- Local LLM orchestration (Mistral 7B via Ollama)
- Deterministic separation between symbolic logic and LLM layer
- FastAPI backend with modular retrieval pipeline

---

## Determinism vs Probabilistic Layer

The architecture deliberately enforces:

| Layer | Nature |
|-------|--------|
| Harmonic modeling | Deterministic |
| Retrieval ranking | Deterministic + weighted blending |
| LLM explanation | Probabilistic but constrained |

This prevents:

- Hallucinated harmonic logic
- Structural invalidity
- Domain drift
- Loss of reproducibility

---

## Why This Is Not a Generic Chatbot

- Knowledge base is structured symbolic data
- No scraped internet corpus
- Retrieval is schema-aware
- Ranking includes domain constraints
- LLM is non-authoritative
- Computation remains outside the LLM

This is an engineering architecture for domain-bound AI — not conversational improvisation.

---

## MIR Perspective

From a Music Information Retrieval standpoint, this system demonstrates:

- Large-scale modal compatibility graph indexing
- Hybrid retrieval over structured harmonic objects
- Deterministic symbolic-to-language bridging
- Controlled LLM integration without surrendering structural integrity
- Explainable AI over formal music systems

It exemplifies a structure-first approach to AI-assisted MIR systems.

---

## Implementation Philosophy

- The dataset holds the knowledge.
- Retrieval selects.
- Ranking refines.
- The LLM verbalizes.

The architecture treats language modeling as an interface layer — not as a reasoning engine.

---

## Intended Audience

- AI Engineers designing domain-aware RAG systems
- MIR researchers exploring symbolic + LLM integration
- Developers building hybrid semantic-symbolic retrieval systems
- Researchers working on explainable AI over structured domains

---

## Publication Status

This system has not been released as a production service.

The reason is architectural integrity.

While the retrieval layer and backend pipeline are fully functional,
the action layer (i.e., deterministic technical or numerical execution
driven through the LLM interface) was intentionally not finalized.

In testing, the LLM could not reliably perform structured numeric or
domain-constrained transformations without introducing ad-hoc prompt
patching or model-dependent behavior.

Rather than shipping a system that:

- Relied on fragile prompt engineering
- Introduced probabilistic drift in deterministic tasks
- Required paid, high-end LLM infrastructure to stabilize behavior
- Blurred the boundary between symbolic computation and language modeling

the system was kept in a research state.

The architectural principle remains:

Deterministic computation must stay deterministic.

The LLM is a verbalization layer — not a numerical engine.

Until a clean separation between symbolic execution and language generation
can be maintained without ad-hoc patches, the system remains intentionally unpublished.

---

## Closing Statement

This architecture demonstrates that:

Retrieval-Augmented Generation can operate over formal symbolic systems  
without sacrificing determinism to probabilistic language modeling.

It is not generative-first.

It is structure-first.

