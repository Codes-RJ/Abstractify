# Issue #25: 🌐 PubMed & MEDLINE Biomedical Search Integration (E-Utilities API)

**Labels**: `enhancement`, `help wanted`, `backend`, `area: api`, `difficulty: easy`  
**Difficulty**: `Easy`  
**Target Files**: `netlify/functions/search/sources/pubmed.ts` (NEW), [`netlify/functions/search.ts`](../netlify/functions/search.ts)

---

## 📌 Problem & Context

AbstractiFy currently relies solely on Semantic Scholar and OpenAlex. For biomedical, pharmacological, genomic, and clinical health sciences queries, PubMed/MEDLINE remains the global gold standard with over 36M curated publications.

---

## 🎯 Goal

Implement a native connector for the **NCBI E-Utilities REST API** (`esearch.fcgi` and `efetch.fcgi`) to directly retrieve peer-reviewed biomedical literature and clinical trial metadata.

---

## ⚙️ Technical Specification

### 1. PubMed Source Adapter (`netlify/functions/search/sources/pubmed.ts`)
- **Query Pipeline**:
  1. `https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&term={query}&retmode=json&retmax=25`
  2. Extract PubMed IDs (`idlist`).
  3. Fetch full article summaries: `https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esummary.fcgi?db=pubmed&id={ids}&retmode=json`
- Map NCBI JSON fields to unified `Paper` interface:
  - `id`: `PMID:${item.uid}`
  - `title`: Article title (stripped of HTML tags)
  - `authors`: Array of author names (`item.authors[].name`)
  - `year`: Publication year from `pubdate`
  - `abstract`: Retrieved via summary or linked PMC abstract
  - `doi`: Extracted from `articleids` where `idtype === 'doi'`

### 2. Search Integration
- Wire adapter into multi-source search pipeline with graceful timeout handling (2.5s limit).

---

## ✅ Acceptance Criteria

- [ ] Create `pubmed.ts` implementing `searchPubMed(query: string, limit?: number): Promise<Paper[]>`.
- [ ] Correctly parse NCBI E-Utilities JSON structure and handle empty results.
- [ ] Sanitize HTML/XML entities from paper titles.
- [ ] Add unit test with mocked NCBI API responses.

---

## 💡 Code Guidance

- NCBI E-Utilities recommends including `tool=AbstractiFy&email=research@abstractify.app` query parameters to prevent IP throttling.
