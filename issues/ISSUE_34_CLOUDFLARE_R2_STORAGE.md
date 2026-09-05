# Issue #34: ☁️ Cloudflare R2 Persistent PDF & Export Artifact Storage

**Labels**: `enhancement`, `help wanted`, `backend`, `area: storage`, `difficulty: medium`  
**Difficulty**: `Medium`  
**Target Files**: `netlify/functions/shared/storage.ts` (NEW), `netlify/functions/pdf-upload.ts` (MODIFY), [`.env.example`](../.env.example)

---

## 📌 Problem & Context

Uploaded PDFs in AbstractiFy are currently kept in ephemeral memory and discarded once the serverless function exits. Users cannot revisit previously analyzed papers across devices or download compiled review artifacts after closing their browser.

---

## 🎯 Goal

Integrate **Cloudflare R2** (S3-compatible object storage with zero egress fees and a generous 10GB free tier) to reliably persist uploaded PDFs, extracted figures, and generated research reports.

---

## ⚙️ Technical Specification

### 1. S3-Compatible Storage Client (`netlify/functions/shared/storage.ts`)
- Use `@aws-sdk/client-s3` configured with Cloudflare R2 endpoint:
  ```
  https://<ACCOUNT_ID>.r2.cloudflarestorage.com
  ```
- Methods:
  - `uploadArtifact(key: string, buffer: Buffer, contentType: string): Promise<string>`
  - `getDownloadUrl(key: string, expiresIn?: number): Promise<string>`
  - `deleteArtifact(key: string): Promise<void>`

### 2. PDF Ingestion Pipeline
- Upon user upload, stream PDF to R2 bucket `abstractify-user-papers/{sessionId}/{hash}.pdf`.
- Store public or signed download URL in Redis session metadata.

---

## ✅ Acceptance Criteria

- [ ] Implement typed R2 storage manager in `storage.ts`.
- [ ] Connect `pdf-upload.ts` to persist files to R2 before parsing.
- [ ] Expose pre-signed download URLs for stored papers and exported synthesis files.
- [ ] Document R2 account configuration in `.env.example`.
- [ ] Add unit test with mocked S3 client.
