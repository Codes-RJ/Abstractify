# Issue #22: 🗄️ Upstash Redis Integration (Session Persistence & Distributed Cache)

**Labels**: `enhancement`, `help wanted`, `backend`, `area: database`, `difficulty: medium`  
**Difficulty**: `Medium`  
**Target Files**: `netlify/functions/shared/redis.ts` (NEW), `netlify/functions/sessions.ts` (NEW), [`netlify/functions/_utils.ts`](../netlify/functions/_utils.ts), [`.env.example`](../.env.example)

---

## 📌 Problem & Context

Currently, AbstractiFy is entirely ephemeral in serverless execution. Search results, paper metadata, extracted comparison matrices, and consensus scores disappear as soon as the client refreshes or closes the tab. Furthermore, repeated queries to external academic APIs (Semantic Scholar, OpenAlex) create unnecessary latency and risk hitting rate limits.

---

## 🎯 Goal

Integrate **Upstash Redis** (serverless Redis via REST API) into Netlify serverless functions to provide persistent research session storage, fast distributed caching for external API responses, and rate limit tracking.

---

## ⚙️ Technical Specification

### 1. Redis Client Adapter (`netlify/functions/shared/redis.ts`)
- Instantiate `@upstash/redis` using environment variables `UPSTASH_REDIS_REST_URL` and `UPSTASH_REDIS_REST_TOKEN`.
- Provide typed helper utilities:
  - `cacheGet<T>(key: string): Promise<T | null>`
  - `cacheSet<T>(key: string, value: T, ttlSeconds?: number): Promise<void>`
  - `saveSession(sessionId: string, sessionData: object): Promise<void>`
  - `getSession(sessionId: string): Promise<object | null>`

### 2. Session Management Endpoint (`netlify/functions/sessions.ts`)
- **Route**: `/.netlify/functions/sessions`
- `GET /api/sessions?id={sessionId}` — Retrieve saved session data (query, papers, consensus, matrix, notes).
- `POST /api/sessions` — Create or update a session with unique UUID.
- `DELETE /api/sessions?id={sessionId}` — Clean up saved session.

### 3. API Response Caching
- Cache search results under `cache:search:{queryHash}` with a 24-hour TTL.
- Cache paper details under `cache:paper:{doiOrId}` with a 7-day TTL.

---

## ✅ Acceptance Criteria

- [ ] Implement robust `@upstash/redis` client with automatic fallback when credentials are not configured.
- [ ] Implement CRUD API for research sessions in `sessions.ts`.
- [ ] Wrap `search.ts` and `consensus.ts` with Redis caching layer to eliminate redundant API calls.
- [ ] Add `UPSTASH_REDIS_REST_URL` and `UPSTASH_REDIS_REST_TOKEN` to `.env.example`.
- [ ] Write integration test suite verifying cache set/get/expiry behavior.

---

## 💡 Code Guidance

- Install `@upstash/redis`.
- Ensure serverless execution handles network timeouts gracefully without blocking the main thread.
