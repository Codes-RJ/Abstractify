# Issue #32: 🤖 Multi-Agent Orchestrator System (Specialized Sub-Agents)

**Labels**: `enhancement`, `help wanted`, `backend`, `area: ai`, `difficulty: hard`  
**Difficulty**: `Hard`  
**Target Files**: `netlify/functions/agent/sub-agents/` (NEW), `netlify/functions/agent/orchestrator.ts` (NEW), [`netlify/functions/_utils.ts`](../netlify/functions/_utils.ts)

---

## 📌 Problem & Context

Monolithic LLM prompts struggle when tasked with conducting multi-step research: searching literature, cross-verifying methodologies, detecting statistical contradictions, and drafting academic prose simultaneously. Dividing responsibilities into specialized sub-agents dramatically improves precision and reduces hallucinations.

---

## 🎯 Goal

Build a **Multi-Agent Orchestrator System** powered by Gemini 2.5 Flash and Google Agent Development Kit (ADK) principles, featuring 5 specialized sub-agents coordinating under an executive controller.

---

## ⚙️ Technical Specification

### 1. Sub-Agent Roster (`netlify/functions/agent/sub-agents/`)
- **Search Agent (`search-agent.ts`)**: Evaluates query domain and selects optimal academic APIs (PubMed, arXiv, DBLP, etc.), manages deduplication and re-ranking.
- **Analysis Agent (`analysis-agent.ts`)**: Evaluates methodology strength, sample size adequacy, and extracts consensus stances.
- **Writing Agent (`writing-agent.ts`)**: Drafts structured literature reviews, abstracts, and bulleted takeaways in academic tone.
- **Methodology Agent (`methodology-agent.ts`)**: Critiques study limitations, compares datasets, and highlights confounding variables.
- **Memory Agent (`memory-agent.ts`)**: Interfaces with Redis and Vector DB to inject user preferences and historical reasoning traces.

### 2. Orchestration Controller (`orchestrator.ts`)
- Master ReAct supervisor receiving user instructions, assigning sub-tasks, and compiling multi-agent outputs into a coherent narrative.

---

## ✅ Acceptance Criteria

- [ ] Implement all 5 specialized sub-agents with distinct system prompts and tools.
- [ ] Implement central supervisor routing tasks sequentially or in parallel.
- [ ] Expose unified API endpoint `/api/agent/multi-query`.
- [ ] Emit sub-agent lifecycle events (e.g. `agent:start`, `agent:done`) for frontend visualizers.
- [ ] Add unit tests testing mock sub-agent delegation.
