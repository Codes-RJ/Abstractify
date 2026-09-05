# Issue #29: 🧪 Europe PMC & bioRxiv Life Sciences Search Integration

**Labels**: `enhancement`, `help wanted`, `backend`, `area: api`, `difficulty: easy`  
**Difficulty**: `Easy`  
**Target Files**: `netlify/functions/search/sources/europe-pmc.ts` (NEW), `netlify/functions/search/sources/biorxiv.ts` (NEW)

---

## 📌 Problem & Context

Life sciences, molecular biology, genetics, and biotechnology researchers require access to both preprints (bioRxiv / medRxiv) and European open-access life sciences literature (Europe PMC), which indexing engines often miss.

---

## 🎯 Goal

Implement dedicated adapters for:
1. **Europe PMC RESTful Web Service**: Over 40 million scientific articles and clinical guidelines.
2. **bioRxiv / medRxiv Content API**: Cutting-edge preprints in biology and health sciences.

---

## ⚙️ Technical Specification

### 1. Europe PMC Adapter (`netlify/functions/search/sources/europe-pmc.ts`)
- **Endpoint**: `https://www.ebi.ac.uk/europepmc/webservices/rest/search?query={query}&format=json&pageSize=20&resultType=core`
- Extract title, authorString, journalTitle, pubYear, abstractText, pmid, pmcid, and openAccess status.

### 2. bioRxiv API Adapter (`netlify/functions/search/sources/biorxiv.ts`)
- **Endpoint**: `https://api.biorxiv.org/details/biorxiv/{doi}` or query endpoint.
- Extract version, category, license, and server (`biorxiv` vs `medrxiv`).

---

## ✅ Acceptance Criteria

- [ ] Implement `europe-pmc.ts` adapter supporting query search and PMCID parsing.
- [ ] Implement `biorxiv.ts` connector with date filtering.
- [ ] Transform outputs into standard `Paper[]` data models.
- [ ] Add unit tests verifying both Europe PMC and bioRxiv response parsers.
