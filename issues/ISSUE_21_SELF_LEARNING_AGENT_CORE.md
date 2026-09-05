# Issue #21: 🧠 Self-Learning Agent Core (Dual Memory & Adaptive Prompts)

**Labels**: `enhancement`, `help wanted`, `backend`, `area: ai`, `difficulty: hard`  
**Difficulty**: `Hard`  
**Target Files**: `netlify/functions/agent/orchestrator.ts` (NEW), `netlify/functions/agent/memory.ts` (NEW), `netlify/functions/agent/self-reflect.ts` (NEW), [`public/app.js`](../public/app.js)

---

## 📌 Problem & Context

Current AI research assistants operate in stateless silos. Each query or conversation begins from a blank slate, failing to retain user preferences, successful reasoning patterns, or learned domain knowledge. A truly intelligent research companion must self-learn from interaction outcomes, remember past discoveries, and calibrate prompts adaptively over time.

---

## 🎯 Goal

Build a **Self-Learning Agent Core** leveraging a dual-memory model (short-term Redis state + long-term vector embeddings) and post-inference self-reflection loops. The agent evaluates its own answers, refines prompts, and accumulates domain expertise across sessions.

---

## ⚙️ Technical Specification

### 1. Dual-Memory Engine (`netlify/functions/agent/memory.ts`)
- **Short-Term Memory (Redis)**:
  - Preserves immediate conversation history, paper citations, and tool call traces.
  - Key schema: `agent:{sessionId}:stm` with TTL of 2 hours.
- **Long-Term Memory (Upstash Vector)**:
  - Vectorizes and stores high-scoring reasoning chains and user-approved synthesis summaries.
  - Queries top-$k$ relevant past reasoning steps during new hypothesis inquiries.

### 2. Self-Reflection Loop (`netlify/functions/agent/self-reflect.ts`)
- Following agent synthesis, dispatch an asynchronous evaluation prompt via Gemini 2.5 Flash:
  - Rubric: Evidence attribution, logical coherence, hallucination absence, methodological precision.
  - Generates an epistemic confidence score ($0.0 - 1.0$) and reflections stored in vector memory.
  - Automatically triggers refined re-prompting if score falls below $0.70$.

### 3. Agent Orchestrator (`netlify/functions/agent/orchestrator.ts`)
- Implements the ReAct (Reason + Act) loop using Gemini function calling.
- Dynamically injects recalled long-term reasoning exemplars into the system instructions.

---

## ✅ Acceptance Criteria

- [ ] Implement `memory.ts` supporting dual-memory storage and recall (STM + LTM).
- [ ] Implement `self-reflect.ts` calculating structured feedback and confidence scores.
- [ ] Create `orchestrator.ts` managing ReAct iterations and tool dispatches.
- [ ] Expose `POST /api/agent/query` returning answer, reasoning chain, and confidence rating.
- [ ] Add comprehensive unit tests in `netlify/functions/agent/__tests__/agent.test.ts`.

---

## 💡 Code Guidance

- Utilize `@google/genai` with `gemini-2.5-flash` model.
- Store long-term memories using 768-dimensional embeddings generated via `gemini-embedding-2`.
