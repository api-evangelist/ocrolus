---
name: Calculate income for mortgage underwriting with Ocrolus
description: Upload income documents to a Book and retrieve income summary and income calculations.
api: openapi/ocrolus-income-openapi.json
operations: [grant-authentication-token, create-a-book, upload-pdf-to-book, book-status, income-summary, income-calculations]
generated: '2026-07-20'
method: generated
---

# Calculate income for mortgage underwriting with Ocrolus

Ocrolus **Income** produces borrower income figures from uploaded documents
(pay stubs, tax forms, bank statements) in a Book.

## Steps

1. **Authenticate** — `grant-authentication-token` (`POST https://auth.ocrolus.com/oauth/token`).
2. **Create a Book** — `create-a-book` (`POST /v1/book/add`).
3. **Upload income documents** — `upload-pdf-to-book` (`POST /v1/book/upload`);
   for pay stubs specifically you may use `upload-paystub`
   (`POST /v2/book/{book_uuid}/document/paystub`).
4. **Wait** — `book-status` (`GET /v1/book/status`) or the `book.income.generated`
   webhook.
5. **Read income summary** — `income-summary` (`GET /v2/book/{book_uuid}/income/summary`).
6. **Read detailed calculations** — `income-calculations`
   (`GET /v2/book/{book_uuid}/income-calculations`). Optionally set guidelines with
   `save-income-guideline` (`PUT /v2/book/{book_uuid}/income-guideline`).

## Rules

- Processing is async; a `425` from income endpoints means results aren't ready — poll or use webhooks.
- Backoff + 20 req/s org limit; JSON error envelope `{status, message, code}`.
