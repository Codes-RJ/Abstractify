# Issue #33: ⚡ Real-Time SSE Streaming & Thought Traces Visualizer UI

**Labels**: `enhancement`, `help wanted`, `frontend`, `backend`, `streaming`, `area: ai`, `difficulty: medium`  
**Difficulty**: `Medium`  
**Target Files**: `netlify/functions/agent/stream.ts` (NEW), `public/js/stream-renderer.js` (NEW), [`public/app.js`](../public/app.js), [`public/styles.css`](../public/styles.css)

---

## 📌 Problem & Context

Waiting 10-15 seconds for a complex AI research response with a simple spinning loader leads to perceived sluggishness. Modern AI interfaces provide instant gratification through token streaming, live tool execution indicators, and collapsible "thinking" reasoning traces.

---

## 🎯 Goal

Implement **Server-Sent Events (SSE) streaming** for all agent interactions, paired with a sleek, typewriter-animated frontend component displaying thought traces and real-time tool badges.

---

## ⚙️ Technical Specification

### 1. Server-Sent Events Endpoint (`netlify/functions/agent/stream.ts`)
- Returns response with headers:
  ```
  Content-Type: text/event-stream
  Cache-Control: no-cache
  Connection: keep-alive
  ```
- Event Protocol:
  - `event: thinking` — Intermediate reasoning thoughts.
  - `event: tool_call` — Notification of tool execution (e.g. `querying PubMed...`).
  - `event: text_delta` — Incremental tokens streaming to the UI.
  - `event: done` — Completion signal with final usage and confidence metrics.

### 2. Frontend Stream Visualizer (`public/js/stream-renderer.js`)
- Consume stream via `fetch()` with `ReadableStreamDefaultReader` or `EventSource`.
- Render live typewriter effect with blinking cursor.
- Interactive collapsible accordion displaying "🧠 Agent Thought Process".

---

## ✅ Acceptance Criteria

- [ ] Implement Netlify streaming function supporting SSE event protocol.
- [ ] Connect Gemini streaming generator via `@google/genai` or Vercel AI SDK.
- [ ] Implement client-side stream reader with automatic reconnect and error handling.
- [ ] Style thought trace cards with subtle dark/light glassmorphism accents.
- [ ] Unit tests for event serialization and reader parsing.
