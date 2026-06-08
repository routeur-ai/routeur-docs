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

## Errors during a stream

For streamed requests (`"stream": true`) the HTTP status is committed once the
first chunk is sent. A failure after that point cannot change the status, so it
arrives as a terminal SSE event and the stream then closes:

```
event: error
data: {"error":{"code":"upstream_error","message":"...","type":"routeur_error"},"request_id":"01K..."}
```

A failure *before* the first chunk (auth, routing, output-moderation block on a
moderated org, or an upstream that produced no bytes) is returned as the
ordinary JSON error above with its normal status code — no stream is opened.

Two stream-specific cases to handle client-side:

- **Mid-stream `error` event.** Treat it like the equivalent HTTP error code:
  `upstream_error` is retryable, a moderation block is not.
- **Truncated stream (no terminal `error` and no `[DONE]`).** A deploy or
  scale-in can cut a long-lived stream. This is expected operationally; retry
  the request idempotently.

## Retry guidance

- **Retry:** `429`, `502`, `504` with exponential backoff; a mid-stream
  `upstream_error` event; a truncated stream.
- **Do not retry:** `400`, `401`, `403`. The request will keep failing.
