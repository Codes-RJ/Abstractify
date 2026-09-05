# Issue #31: 🔓 Unpaywall Real-Time Open Access PDF Link Resolver

**Labels**: `enhancement`, `help wanted`, `backend`, `area: api`, `difficulty: easy`  
**Difficulty**: `Easy`  
**Target Files**: `netlify/functions/search/sources/unpaywall.ts` (NEW), `netlify/functions/oa-resolver.ts` (NEW)

---

## 📌 Problem & Context

Millions of research papers published in traditional closed-access journals actually have legal, author-archived green open-access versions hosted on institutional repositories. Without an automated resolver, users assume these papers are locked behind a $40 paywall.

---

## 🎯 Goal

Implement an automated **Unpaywall API Resolver** that takes any DOI and returns the legal direct PDF link and open access license (CC-BY, bronze, gold, hybrid, green).

---

## ⚙️ Technical Specification

### 1. Unpaywall Resolver (`netlify/functions/search/sources/unpaywall.ts`)
- **Endpoint**: `https://api.unpaywall.org/v2/{doi}?email=oa@abstractify.app`
- Extract:
  - `is_oa`: Boolean
  - `oa_status`: gold | green | bronze | hybrid | closed
  - `best_oa_location.url_for_pdf`: Direct PDF download link
  - `best_oa_location.version`: publishedVersion | acceptedVersion

### 2. Standalone API Endpoint (`netlify/functions/oa-resolver.ts`)
- `GET /api/oa-resolver?doi={doi}`
- Returns:
  ```json
  {
    "doi": "10.1038/nature12373",
    "isOpenAccess": true,
    "pdfUrl": "https://repository.cam.ac.uk/...",
    "license": "cc-by",
    "version": "publishedVersion"
  }
  ```

---

## ✅ Acceptance Criteria

- [ ] Implement `unpaywall.ts` client with valid contact email.
- [ ] Create `/api/oa-resolver` endpoint.
- [ ] Add direct "Read Free PDF" badge on UI paper cards when open access link exists.
- [ ] Cache resolved DOIs in Redis for 14 days to minimize rate hits.
- [ ] Add unit test suite with mock Unpaywall payloads.
