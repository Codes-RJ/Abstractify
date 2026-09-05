# Issue #28: 🔗 Crossref DOI Resolution & Retraction Status Check

**Labels**: `enhancement`, `help wanted`, `backend`, `area: api`, `difficulty: easy`  
**Difficulty**: `Easy`  
**Target Files**: `netlify/functions/search/sources/crossref.ts` (NEW), `netlify/functions/doi-check.ts` (NEW)

---

## 📌 Problem & Context

Scientific integrity depends on knowing whether a cited study remains valid or has undergone editorial correction, expression of concern, or full retraction. Additionally, DOI lookups need a canonical source for definitive bibliographic data.

---

## 🎯 Goal

Create a **Crossref API Connector** that performs DOI lookups, extracts citation counts from official publishers, and flags retracted papers using Crossmark metadata.

---

## ⚙️ Technical Specification

### 1. Crossref Connector (`netlify/functions/search/sources/crossref.ts`)
- **Endpoint**: `https://api.crossref.org/works/{doi}` or `https://api.crossref.org/works?query={query}&rows=20`
- Include polite headers: `User-Agent: AbstractiFy/2.0 (mailto:dev@abstractify.app)` to join Crossref's high-speed polite pool.
- Extract:
  - Publisher name, container-title (journal), ISSN, published-print/online dates.
  - `is-referenced-by-count`: Official citation count.
  - `update-to`: Array indicating errata, corrections, or retractions.

### 2. Retraction & Reliability Verification (`netlify/functions/doi-check.ts`)
- Endpoint: `GET /api/doi-check?doi={doi}`
- Returns:
  ```json
  {
    "doi": "10.1016/...",
    "isRetracted": false,
    "hasCorrections": true,
    "status": "valid",
    "publisher": "Elsevier BV",
    "indexedDate": "2026-01-15"
  }
  ```

---

## ✅ Acceptance Criteria

- [ ] Implement `crossref.ts` with polite pool headers.
- [ ] Implement `doi-check.ts` detecting retraction tags and expressions of concern.
- [ ] Add visual retraction warning badge in paper cards if paper is flagged.
- [ ] Unit tests for both standard and retracted paper DOIs.
