# IR2025 Retrieval — BM25 Baseline vs Semantic Search (MiniLM + FAISS)



> **Goal:** Build and evaluate an Information Retrieval system on the IR2025 dataset, comparing a strong lexical baseline (**BM25**) with a semantic pipeline (**Transformer embeddings + FAISS**) using **TREC** run files and **trec_eval** metrics.

---

## Table of Contents
- [Overview](#overview)
- [Methods](#methods)
- [Results](#results)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Running Phase 2 (Semantic)](#running-phase-2-semantic)
- [Evaluation (trec_eval)](#evaluation-trec_eval)
- [Reproducibility Notes](#reproducibility-notes)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Overview

This repository contains:
- **Phase 1 (Baseline):** BM25 retrieval → produces **TREC run files** and evaluation summaries.
- **Phase 2 (Semantic Retrieval):** `all-MiniLM-L6-v2` embeddings + **FAISS** ANN search (cosine similarity).
- **Evaluation:** `trec_eval` reports **MAP** and **Precision@k** (P@5/10/15/20), including per-query outputs.

The main deliverable is a clean comparison table (Phase 1 vs Phase 2) produced from the same dataset and relevance judgments.

---


# Descriprion

- Built a semantic and lexical information retrieval pipeline using sentence embeddings (all-MiniLM-L6-v2), batching, mean pooling, and L2 normalization. 
- Indexed document vectors with FAISS (IndexFlatIP) for efficient top-k retrieval per query. 
- Generated TREC run files and evaluated performance using trec_eval (MAP, Precision@k). 
- Benchmarked against a BM25 baseline, achieving MAP 0.64 (BM25@50) and MAP 0.26 (Semantic@50), highlighting dataset sensitivity and retrieval trade-offs. 



## Methods

### Phase 1 — BM25 (Lexical)
A classic term-based baseline:
- Ranks documents based on token overlap and term statistics (BM25)
- Produces run files for multiple cutoffs (e.g., **k = 20 / 30 / 50**)

### Phase 2 — Semantic Retrieval (Transformer + FAISS)
End-to-end semantic pipeline:
- **Encoder:** `sentence-transformers/all-MiniLM-L6-v2`
- **Embedding:** mean pooling with attention mask, then **L2 normalization**
- **Similarity:** cosine similarity implemented as inner product on normalized vectors
- **Index:** `faiss.IndexFlatIP`
- Produces run files for **k = 20 / 30 / 50**

---

## Results

**Phase 1 — BM25**
| Run | MAP | P@5 | P@10 | P@15 | P@20 |
|---|---:|---:|---:|---:|---:|
| BM25 @20 | 0.5463 | 0.82 | 0.67 | 0.58 | 0.525 |
| BM25 @30 | 0.6011 | 0.82 | 0.67 | 0.58 | 0.525 |
| BM25 @50 | 0.6385 | 0.82 | 0.67 | 0.58 | 0.525 |

**Phase 2 — MiniLM + FAISS**
| Run | MAP | P@5 | P@10 | P@15 | P@20 |
|---|---:|---:|---:|---:|---:|
| Semantic @20 | 0.2039 | 0.42 | 0.34 | 0.2933 | 0.28 |
| Semantic @30 | 0.2328 | 0.42 | 0.34 | 0.2933 | 0.28 |
| Semantic @50 | 0.2556 | 0.42 | 0.34 | 0.2933 | 0.28 |

**Interpretation (short):**
- BM25 is very strong on this dataset (high MAP and P@k).
- Semantic retrieval improves with larger k but remains below BM25, indicating the dataset may favor exact/lexical matching or the current semantic setup needs tuning (chunking, longer max_length, reranking, hybrid, etc.).

---

## Project Structure

Suggested structure (matches your outputs):

