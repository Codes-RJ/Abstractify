# Issue #23: 🧬 Upstash Vector DB (Reasoning Chains & Long-Term Memory)

**Labels**: `enhancement`, `help wanted`, `backend`, `area: database`, `area: ai`, `difficulty: medium`  
**Difficulty**: `Medium`  
**Target Files**: `netlify/functions/shared/vector.ts` (NEW), `netlify/functions/recommend.ts` (NEW), [`netlify/functions/search.ts`](../netlify/functions/search.ts), [`.env.example`](../.env.example)

---

## 📌 Problem & Context

Vector embeddings generated in AbstractiFy (via Gemini embedding models) are currently kept in transient memory or calculated on the fly during search. This leads to redundant embedding compute, inability to index millions of papers locally, and lack of persistent semantic memory for AI reasoning traces.

---

## 🎯 Goal

Integrate **Upstash Vector** (serverless vector database) to store and query high-dimensional embeddings for:
1. Long-term agent reasoning patterns and research insights.
2. Ingested PDF chunks across user sessions.
3. Fast semantic paper recommendation and citation matching.

---

## ⚙️ Technical Specification

### 1. Vector Client Adapter (`netlify/functions/shared/vector.ts`)
- Initialize `@upstash/vector` using `UPSTASH_VECTOR_REST_URL` and `UPSTASH_VECTOR_REST_TOKEN`.
- Target 768-dimensional embeddings matching Google `gemini-embedding-2` or text-embedding-004.
- Core functions:
  - `upsertVector(id: string, vector: number[], metadata: Record<string, any>): Promise<void>`
  - `queryVectors(vector: number[], topK?: number, filter?: string): Promise<VectorMatch[]>`
  - `deleteVector(id: string): Promise<void>`

### 2. Indexes & Namespaces
- Namespace `papers`: Semantic embeddings of paper abstracts with metadata (DOI, year, journal).
- Namespace `reasoning`: Proven successful thesis-synthesis chains with user rating.
- Namespace `pdf-chunks`: Persisted vector index for uploaded research papers.

### 3. Recommendation API (`netlify/functions/recommend.ts`)
- Query the user's current paper vector against the `papers` index using Cosine similarity.
- Return top 5 semantically closest related papers with similarity confidence.

---

## ✅ Acceptance Criteria

- [ ] Create `vector.ts` client wrapper supporting upsert, batch upsert, and nearest-neighbor search.
- [ ] Connect PDF chunking pipeline to store vectors in Upstash Vector.
- [ ] Build `/api/recommend` endpoint powered by Vector index lookups.
- [ ] Add vector configuration variables to `.env.example`.
- [ ] Implement fallback to in-memory cosine calculation if Upstash Vector keys are omitted.

---

## 💡 Code Guidance

- Install `@upstash/vector`.
- Metadata filtering allows slicing queries by publication year or topic category.
