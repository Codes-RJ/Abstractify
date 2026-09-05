# Issue #24: 📨 Upstash QStash Async Task Queue (Background Synthesis & Embeddings)

**Labels**: `enhancement`, `help wanted`, `backend`, `devops`, `difficulty: medium`  
**Difficulty**: `Medium`  
**Target Files**: `netlify/functions/shared/queue.ts` (NEW), `netlify/functions/tasks/worker.ts` (NEW), [`.env.example`](../.env.example)

---

## 📌 Problem & Context

Netlify Serverless functions enforce strict execution time boundaries (10-26s timeout). Deep academic operations—such as batch consensus over 50 papers, recursive multi-paper literature reviews, and asynchronous PDF vector extraction—frequently risk timeouts if run synchronously in the client request cycle.

---

## 🎯 Goal

Implement **Upstash QStash** as a serverless message queue to offload long-running background tasks, decouple heavy AI workloads, and notify clients or webhook consumers upon task completion.

---

## ⚙️ Technical Specification

### 1. Queue Publisher (`netlify/functions/shared/queue.ts`)
- Use `@upstash/qstash` client with `QSTASH_TOKEN`.
- Helpers:
  - `publishTask(destinationUrl: string, payload: object, delaySeconds?: number): Promise<string>`
  - `verifySignature(signature: string, body: string): Promise<boolean>`

### 2. Task Worker (`netlify/functions/tasks/worker.ts`)
- Background handler receiving queued events with cryptographic signature verification (`Receiver` from `@upstash/qstash`).
- Supported Task Types:
  - `BATCH_CONSENSUS`: Computes stance across large paper sets and saves result to Redis.
  - `DEEP_LIT_REVIEW`: Compiles comprehensive multi-section Markdown reviews.
  - `EMBED_PDF_CORPUS`: Generates embeddings for large multi-page documents in the background.

---

## ✅ Acceptance Criteria

- [ ] Implement typed QStash task dispatcher in `queue.ts`.
- [ ] Create authenticated worker receiver in `worker.ts` verifying incoming webhooks.
- [ ] Provide client polling or SSE status hook for background job progression.
- [ ] Document QStash setup and environment keys in `.env.example`.
- [ ] Write unit test verifying signature validation and event dispatch logic.

---

## 💡 Code Guidance

- Ensure `verifySignature` properly utilizes `QSTASH_CURRENT_SIGNING_KEY` and `QSTASH_NEXT_SIGNING_KEY`.
