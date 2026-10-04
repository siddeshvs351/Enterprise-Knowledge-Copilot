# Enterprise Knowledge Copilot

A role-aware question-answering system over internal documents. Users ask
questions in natural language and get answers grounded in source documents
with citations. The system only retrieves documents the user's role is
allowed to see, and the permission filter runs before anything reaches
the LLM.

## Why this project

Most RAG demos ignore access control. In a real company, an employee must
never receive an answer derived from a manager-only or admin-only
document. This project is built around that constraint and measures it:
**unauthorized-document retrieval rate = 0%**.

## Features

- **Hybrid retrieval:** BM25 + vector search fused with Reciprocal Rank Fusion
- **Reranking:** cross-encoder re-scores the top candidates
- **RBAC:** JWT auth; role comes from the token, never the request body
- **ACL filtering:** applied in the retrieval query and verified again afterwards
- **Citations:** every claim in the answer references its source chunk
- **Role-keyed semantic cache:** cache keys include the role, so one role's
  cached answer is never served to another
- **Observability:** OpenTelemetry traces (Jaeger) and Prometheus metrics
- **Evaluation:** [N]-question golden set, Recall@5, MRR, RAGAS faithfulness,
  and an automated security test

## Architecture

**Offline:** Docs → chunk + role metadata → BM25 index + vector index

**Online:** User + JWT → auth → semantic cache → hybrid retrieve →
ACL filter → rerank → LLM with citations

## Results

| Pipeline | Recall@5 | MRR | p95 latency | Cost / answer |
|---|---|---|---|---|
| Vector only | [ ] | [ ] | [ ] ms | $[ ] |
| Hybrid | [ ] | [ ] | [ ] ms | $[ ] |
| Hybrid + rerank | [ ] | [ ] | [ ] ms | $[ ] |

Unauthorized chunks retrieved across [N] adversarial queries: **0**

## Tech stack

FastAPI, PyJWT, Qdrant, rank_bm25, bge-small-en, bge-reranker-base,
Redis, OpenTelemetry, Jaeger, Prometheus, RAGAS, Docker Compose

## Quick start

    docker compose up
    python eval/run_eval.py

## Known failure modes
1. [Describe a real failure you observed]
2. ...

## Design trade-offs
- Why hybrid over pure vector
- Why the ACL filter runs before the LLM
- Why the cache key includes the role

## What breaks at 10x traffic
[Reranker latency, BM25 moving to OpenSearch, cache invalidation, etc.]

## What I changed from the reference repo
[List your own changes: domain, data, tests, metrics]
