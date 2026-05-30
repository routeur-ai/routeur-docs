---
title: Rate limits
description: Per-key request and token rate limits, response headers, and backoff behaviour.
section: Get started
prev:
  href: /guides/errors
  label: Errors
next:
  href: /guides/request-lifecycle
  label: Request lifecycle
---

Routeur applies two independent caps per API key: **requests per minute** and **tokens per minute** (prompt + completion). Hitting either cap returns `429 rate_limited`.

## Defaults

New keys start with conservative limits. The dashboard shows your current caps and lets you request increases.

:::cards cols=2
- label: Requests / minute
  value: "600"
  desc: Default per-key request cap.
- label: Tokens / minute
  value: "200,000"
  desc: Combined prompt + completion tokens.
:::

## Response headers

Every response includes the current state of both buckets so clients can self-throttle without polling.

:::params
- name: X-Routeur-RateLimit-Requests-Remaining
  type: integer
  desc: Requests left in the current minute window.
- name: X-Routeur-RateLimit-Tokens-Remaining
  type: integer
  desc: Tokens left in the current minute window.
- name: Retry-After
  type: seconds
  note: on 429 only
  desc: Number of seconds to wait before retrying.
:::

## Hitting the limit

:::response status=429 label="caller response"
```json
{
  "error": {
    "code":    "rate_limited",
    "message": "requests per minute exceeded",
    "type":    "routeur_error"
  }
}
```
:::

:::callout amber
**Streaming caveat.** Streamed responses count their completion tokens against your bucket as they're generated. A long stream can therefore push you over the cap mid-response.
:::
