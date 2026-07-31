---
name: Configure Ocrolus webhooks for async completion
description: Register an account- or org-level webhook, rotate its secret, test it, and consume completion events instead of polling.
api: openapi/ocrolus-account-level-webhooks-openapi.json
operations: [grant-authentication-token, configure-webhook, webhook-configuration, test-webhook, add-webhook, list-events]
generated: '2026-07-20'
method: generated
---

# Configure Ocrolus webhooks for async completion

Because Ocrolus processing is asynchronous, webhooks are the reliable way to learn
when a Book or Document finishes — preferred over polling `book-status`.

## Steps

1. **Authenticate** — `grant-authentication-token` (`POST https://auth.ocrolus.com/oauth/token`).
2. **Register a webhook**
   - Account-level: `configure-webhook`
     (`POST /v1/account/settings/update/webhook_endpoint`).
   - Organization-level: `add-webhook` (`POST /webhook`).
3. **Verify configuration** — `webhook-configuration`
   (`GET /v1/account/settings/webhook_details`).
4. **Test delivery** — `test-webhook`
   (`GET /v1/account/settings/test_webhook_endpoint`) or `test-org-webhook`
   (`POST /webhook/{webhook_uuid}/test`).
5. **Rotate the signing secret** — `configure-webhook-secret-account-level`
   (`POST /v1/account/settings/webhook/rotate-secret`) or the org-level equivalent.
6. **Inspect delivery history** — `list-events` (`GET /webhook/{webhook_uuid}/events`).

## Event types to subscribe to

`book.completed`, `book.verified`, `book.classified`, `book.income.generated`,
`book.detect.signal_found`, `document.upload_succeeded`, `document.classification_succeeded`,
`plaid.upload_succeeded`, and their `*_failed` / `*_not_found` counterparts
(full list in `asyncapi/ocrolus-webhooks.yml`).

## Rules

- Webhook handlers must respond within **5 seconds** or the delivery times out —
  offload heavy work to a background worker.
- Verify the HMAC signature using the configured secret before trusting a payload.
