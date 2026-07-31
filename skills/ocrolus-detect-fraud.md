---
name: Detect document fraud signals with Ocrolus
description: Upload documents to a Book and retrieve authenticity/fraud signals and suspicious-activity flags.
api: openapi/ocrolus-detect-openapi.json
operations: [grant-authentication-token, create-a-book, upload-pdf-to-book, book-status, book-fraud-signals, suspicious-activity-flags]
generated: '2026-07-20'
method: generated
---

# Detect document fraud signals with Ocrolus

Ocrolus **Detect** returns authenticity and fraud signals for documents in a Book.

## Steps

1. **Authenticate** — `grant-authentication-token` (`POST https://auth.ocrolus.com/oauth/token`).
2. **Create a Book** — `create-a-book` (`POST /v1/book/add`), keep `book_uuid`.
3. **Upload documents** — `upload-pdf-to-book` (`POST /v1/book/upload`, multipart).
4. **Wait** — `book-status` (`GET /v1/book/status`) or the `book.detect.signal_found` /
   `document.detect.signal_found` webhooks.
5. **Read fraud signals** — `book-fraud-signals`
   `GET /v2/detect/book/{book_uuid}/signals` for book-level signals.
6. **Read suspicious-activity flags** — `suspicious-activity-flags`
   `GET /v1/book/{book_uuid}/suspicious-activity-flags`.

## Rules

- The legacy Business Verification operations (`create-business`, `retrieve-business`,
  `vendor-auth`) are **deprecated** — do not build on them (see `lifecycle/`).
- Backoff + 20 req/s org limit; JSON error envelope `{status, message, code}`.
