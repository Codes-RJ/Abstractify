# Issue #27: 🔬 arXiv Preprint Server Search Integration

**Labels**: `enhancement`, `help wanted`, `backend`, `area: api`, `difficulty: easy`  
**Difficulty**: `Easy`  
**Target Files**: `netlify/functions/search/sources/arxiv.ts` (NEW), [`netlify/functions/search.ts`](../netlify/functions/search.ts)

---

## 📌 Problem & Context

Peer-reviewed publication cycles often take 6 to 18 months. For fast-evolving disciplines like Machine Learning, Quantum Physics, Computer Science, and Mathematics, the newest state-of-the-art breakthroughs appear first on the **arXiv** preprint server.

---

## 🎯 Goal

Build an **arXiv Search Adapter** that queries the official arXiv API (Atom/XML feed), parses preprint metadata, primary categories, and direct PDF links.

---

## ⚙️ Technical Specification

### 1. arXiv Adapter (`netlify/functions/search/sources/arxiv.ts`)
- **Endpoint**: `http://export.arxiv.org/api/query?search_query=all:{query}&start=0&max_results=20&sortBy=relevance`
- Parse XML response into structured JSON objects:
  - `id`: arXiv accession ID (e.g. `arXiv:2310.06825`)
  - `title`: Sanitized paper title
  - `authors`: Array of author names from `<author><name>...</name></author>`
  - `year`: Publication year from `<published>`
  - `abstract`: Text summary from `<summary>`
  - `pdfUrl`: Extracted from `<link title="pdf" ...>`
  - `primaryCategory`: E.g., `cs.AI`, `quant-ph`, `stat.ML`

### 2. Fast Lightweight Parser
- Avoid heavy native XML dependencies; use standard regex or lightweight DOM/XML parser compatible with Netlify Edge / Node functions.

---

## ✅ Acceptance Criteria

- [ ] Implement `searchArxiv(query: string): Promise<Paper[]>` in `arxiv.ts`.
- [ ] Correctly parse XML Atom feeds and extract primary categories.
- [ ] Clean up line breaks and excess whitespaces in arXiv abstracts.
- [ ] Return valid PDF links matching `https://arxiv.org/pdf/{id}.pdf`.
- [ ] Add unit test with recorded arXiv XML mock fixtures.
