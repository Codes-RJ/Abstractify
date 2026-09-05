# Issue #35: 🧩 Multi-Source Paper Deduplicator & Hybrid Cross-Ranker

**Labels**: `enhancement`, `help wanted`, `backend`, `area: api`, `area: ai`, `difficulty: medium`  
**Difficulty**: `Medium`  
**Target Files**: `netlify/functions/search/deduplicator.ts` (NEW), `netlify/functions/search/ranker.ts` (NEW), [`netlify/functions/search.ts`](../netlify/functions/search.ts)

---

## 📌 Problem & Context

Querying 12+ academic APIs simultaneously inevitably yields duplicate papers with slightly different title casings, missing DOIs, or fragmented author lists. Simply concatenating results produces cluttered lists and dilutes citation accuracy.

---

## 🎯 Goal

Build a high-performance **Multi-Source Deduplicator & Hybrid Cross-Ranker** that merges identical publications across databases, combines their metadata, and ranks them using reciprocal rank fusion (RRF) and vector embeddings.

---

## ⚙️ Technical Specification

### 1. Deduplication Engine (`netlify/functions/search/deduplicator.ts`)
- **Primary Match**: Normalized exact DOI comparison (case-insensitive, strip URL prefixes).
- **Secondary Match**: Levenshtein distance title comparison ($\ge 92\%$ similarity threshold) plus matching publication year ($\pm 1$ year).
- **Merge Logic**:
  - Combine citation counts taking $\max(\text{citations})$.
  - Merge open access URLs (prefer direct PDF links from Unpaywall/CORE).
  - Annotate source badges: e.g. `sources: ['PubMed', 'OpenAlex', 'arXiv']`.

### 2. Hybrid Cross-Ranker (`netlify/functions/search/ranker.ts`)
- Combine BM25 keyword relevance, citation velocity, and Gemini vector cosine similarity using Reciprocal Rank Fusion (RRF).

---

## ✅ Acceptance Criteria

- [ ] Implement `deduplicatePapers(papers: Paper[]): Paper[]` in `deduplicator.ts`.
- [ ] Implement Levenshtein title matching with fast early-exit heuristics.
- [ ] Implement hybrid ranker combining vector similarity and citation weights.
- [ ] Verify test suite handles 100+ mixed papers with $\ge 40\%$ duplication in $<50$ms.
