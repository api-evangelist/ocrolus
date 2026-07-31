---
name: Analyze a bank statement with Ocrolus
description: Authenticate, create a Book, upload a bank statement PDF, wait for processing, then pull cash-flow analytics.
api: openapi/ocrolus-file-uploads-openapi.json
operations: [grant-authentication-token, create-a-book, upload-pdf-to-book, book-status, bsic, cash-flow-features]
generated: '2026-07-20'
method: generated
---

# Analyze a bank statement with Ocrolus

Ocrolus processing is asynchronous — you upload documents to a **Book**, wait for
processing to finish, then read analytics. Ground every call in a real operationId.

## Steps

1. **Get an access token** — `grant-authentication-token`
   `POST https://auth.ocrolus.com/oauth/token` with your client credentials
   (`grant_type=client_credentials`). Use the returned `access_token` as
   `Authorization: Bearer <token>` on every subsequent call. Tokens live ~86400s.

2. **Create a Book** — `create-a-book`
   `POST /v1/book/add`. A Book is the container for the documents of one case.
   Keep the returned `book_uuid`.

3. **Upload the statement PDF** — `upload-pdf-to-book`
   `POST /v1/book/upload` as `multipart/form-data` (max 200 MB). Bank statements
   are auto-detected; other doc types take an optional `form_type`.

4. **Wait for completion** — `book-status`
   Poll `GET /v1/book/status` until the Book reports complete, or subscribe to the
   `book.completed` webhook instead (preferred — see the webhooks skill). A `425`
   response from analytics means results are not ready yet.

5. **Read cash-flow analytics** — `bsic` then `cash-flow-features`
   `GET /v2/book/{book_uuid}/income/bank-statement-v2` for the bank-statement
   income/cash-flow view, and `GET /v2/book/{book_uuid}/cash_flow_features` for
   engineered cash-flow features.

## Rules

- Retry on transient errors with exponential backoff (no idempotency key exists).
- Respect the 20 req/s per-organization rate limit (HTTP 429 on breach).
- Errors are a JSON envelope: `{status, message, code}` — see `errors/ocrolus-error-codes.yml`.
