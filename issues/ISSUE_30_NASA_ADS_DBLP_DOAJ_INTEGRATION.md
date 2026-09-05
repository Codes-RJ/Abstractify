# Issue #30: 🔭 NASA ADS, DBLP & DOAJ Specialized Domain Search Integrations

**Labels**: `enhancement`, `help wanted`, `backend`, `area: api`, `difficulty: medium`  
**Difficulty**: `Medium`  
**Target Files**: `netlify/functions/search/sources/nasa-ads.ts` (NEW), `netlify/functions/search/sources/dblp.ts` (NEW), `netlify/functions/search/sources/doaj.ts` (NEW)

---

## 📌 Problem & Context

Specialized disciplines require authoritative indexing engines:
- **Astrophysics & Space Physics**: NASA ADS (Astrophysics Data System).
- **Computer Science & Software Engineering**: DBLP Computer Science Bibliography.
- **Peer-Reviewed Open Access**: DOAJ (Directory of Open Access Journals).

---

## 🎯 Goal

Build three domain-specific search adapters to ensure comprehensive academic coverage across CS, astronomy/physics, and verified open-access publishing.

---

## ⚙️ Technical Specification

### 1. NASA ADS Adapter (`nasa-ads.ts`)
- **Endpoint**: `https://api.adsabs.harvard.edu/v1/search/query?q={query}&fl=id,bibcode,title,author,year,abstract,doi,citation_count`
- Requires `NASA_ADS_TOKEN` (free registration). Gracefully bypasses if not configured.

### 2. DBLP Adapter (`dblp.ts`)
- **Endpoint**: `https://dblp.org/search/publ/api?q={query}&format=json&h=20`
- Completely free, no API key required. High-precision author and CS venue indexing.

### 3. DOAJ Adapter (`doaj.ts`)
- **Endpoint**: `https://doaj.org/api/v2/search/articles/{query}?pageSize=20`
- Provides verified high-quality open-access journal articles with full CC licensing tags.

---

## ✅ Acceptance Criteria

- [ ] Implement `nasa-ads.ts` with token authorization header.
- [ ] Implement `dblp.ts` parsing DBLP publication types (conferences vs journals).
- [ ] Implement `doaj.ts` filtering for quality-controlled open-access articles.
- [ ] Handle missing API tokens without disrupting global search.
- [ ] Unit tests for all three adapters.
