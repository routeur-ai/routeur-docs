---
title: Errors
description: routeur.ai error codes, statuses, and how to handle them.
section: Get started
prev:
  href: /guides/authentication
  label: Authentication
next:
  href: /guides/rate-limits
  label: Limits and quotas
---

routeur.ai returns short, JSON-shaped errors with a stable `error.code` and a human-readable `error.message`. Raw upstream provider error bodies are never relayed to callers — you'll always see a routeur.ai-shaped envelope.

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
- status: "400"
  code: embeddings_unsupported
  desc: The resolved model does not support embeddings.
- status: "402"
  code: billing_inactive
  desc: The organisation's subscription is cancelled. See [Limits and quotas](/guides/rate-limits).
- status: "403"
  code: blocked_by_dlp
  desc: A DLP policy with the Block action matched the request, which was stopped before the upstream call. The matching policy slug is in `error.message`. See [DLP](/guides/dlp).
- status: "403"
  code: blocked_by_shield
  desc: A prompt-shield rule matched the input — for example an instruction-override or encoded-payload attempt.
- status: "403"
  code: blocked_by_classifier
  desc: A classifier rule's score threshold was exceeded on the input.
- status: "403"
  code: blocked_by_moderation
  desc: The upstream model answered but output moderation blocked the response from reaching the caller.
- status: "404"
  code: model_not_found
  desc: Unknown model id, or a model not activated for your workspace.
- status: "413"
  code: body_too_large
  desc: Request body exceeded the cap — 5 MiB for chat completions, 10 MiB for embeddings.
- status: "429"
  code: allowance_exceeded
  desc: Monthly allowance and overage guardrail both reached. See [Limits and quotas](/guides/rate-limits).
- status: "429"
  code: upstream_rate_limited
  desc: The upstream provider rate-limited the call. Retry with backoff.
- status: "502"
  code: provider_unconfigured
  desc: No credential is configured for the resolved provider.
- status: "502"
  code: provider_unavailable
  desc: The upstream provider could not be reached.
- status: "502"
  code: upstream_error
  desc: Non-timeout upstream failure.
- status: "503"
  code: policy_invalid
  desc: "A DLP, prompt-shield or moderation policy that covers the request has a pattern the gateway cannot compile, so it fails closed. The message names the policy. Fix the pattern in the admin panel. Not retryable until fixed. A Log or Alert DLP policy with a bad pattern is skipped instead. See [DLP](/guides/dlp#when-a-pattern-cannot-be-compiled)."
- status: "503"
  code: overloaded
  desc: The gateway's in-flight cap was reached. Carries `Retry-After`; retry with jittered backoff.
- status: "504"
  code: upstream_error
  desc: The upstream call hit routeur.ai's per-request timeout. Safe to retry idempotently.
:::

There is no `rate_limited` code and no `forbidden` code. A revoked key returns
`401 unauthorized`, the same as an unknown one.

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
- **Do not retry:** `400`, `401`, `403`, and `503 policy_invalid`. The request will keep failing until the policy is fixed. A `503 overloaded` is retryable.
