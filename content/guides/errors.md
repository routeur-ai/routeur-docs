---
title: Errors
description: Routeur error codes, statuses, and how to handle them.
section: Get started
prev:
  href: /guides/authentication
  label: Authentication
next:
  href: /guides/rate-limits
  label: Rate limits
---

Routeur returns short, JSON-shaped errors with a stable `error.code` and a human-readable `error.message`. Raw upstream provider error bodies are never relayed to callers — you'll always see a routeur-shaped envelope.

## Error shape

:::code label="response body"
```json
{
  "error": {
    "code":    "blocked_by_dlp",
    "message": "dlp:credit_card_redact",
    "type":    "routeur_error"
  }
}
```
:::

## Status & codes

:::params
- status: "400"
  code: invalid_request
  desc: Malformed JSON or missing required fields such as `messages`.
- status: "401"
  code: unauthorized
  desc: Missing or unknown routeur.ai bearer token. See [Authentication](/guides/authentication).
- status: "403"
  code: blocked_by_dlp
  desc: Input safety or DLP blocked the request before the upstream call. The matching rule name is in `error.message`.
- status: "403"
  code: blocked_by_moderation
  desc: The upstream model answered but output moderation blocked the response from reaching the caller.
- status: "429"
  code: rate_limited
  desc: Per-key request or token rate cap exceeded. See [Rate limits](/guides/rate-limits).
- status: "502"
  code: upstream_error
  desc: Non-timeout upstream failure or `provider_unconfigured` when no provider key is mounted.
- status: "504"
  code: upstream_error
  desc: The upstream call hit routeur.ai's per-request timeout. Safe to retry idempotently.
:::

## Retry guidance

- **Retry:** `429`, `502`, `504` with exponential backoff.
- **Do not retry:** `400`, `401`, `403`. The request will keep failing.
