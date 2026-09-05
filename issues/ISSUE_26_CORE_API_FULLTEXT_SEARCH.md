# Issue #26: 📚 CORE API Open-Access Full-Text Search Integration

**Labels**: `enhancement`, `help wanted`, `backend`, `area: api`, `difficulty: easy`  
**Difficulty**: `Easy`  
**Target Files**: `netlify/functions/search/sources/core-api.ts` (NEW), [`netlify/functions/search.ts`](../netlify/functions/search.ts), [`.env.example`](../.env.example)

---

## 📌 Problem & Context

Researchers frequently hit paywalls when attempting to read beyond abstracts. CORE aggregates over 300 million open-access research papers from global repositories and journals, providing direct links to freely accessible full-text PDFs.

---

## 🎯 Goal

Integrate the **CORE API v3** search endpoint to fetch open-access research papers, full-text URLs, and institutional repository identifiers.

---

## ⚙️ Technical Specification

### 1. CORE API Connector (`netlify/functions/search/sources/core-api.ts`)
- **Endpoint**: `https://api.core.ac.uk/v3/search/works`
- **Method**: `POST` or `GET` with header `Authorization: Bearer ${CORE_API_KEY}`
- **Parameters**: `q={query}&limit=20`
- Extract metadata:
  - `id`: `core_${item.id}`
  - `title`: Article title
  - `authors`: Array of strings
  - `year`: `yearPublished`
  - `abstract`: Full text abstract
  - `downloadUrl`: Direct open access PDF download link
  - `doi`: DOI string

### 2. Fallback Handling
- If `CORE_API_KEY` is omitted from the environment, silently skip CORE search without throwing errors.

---

## ✅ Acceptance Criteria

- [ ] Implement `core-api.ts` connector returning normalized `Paper[]`.
- [ ] Handle pagination and rate limit responses (HTTP 429).
- [ ] Attach `downloadUrl` to the `Paper` model for direct PDF retrieval.
- [ ] Document `CORE_API_KEY` in `.env.example`.
- [ ] Add unit tests verifying parsing and key validation.
