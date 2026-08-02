---
title: Limits and quotas
description: How routeur.ai governs throughput — plan allowances, overage, and gateway backpressure.
section: Get started
prev:
  href: /guides/errors
  label: Errors
next:
  href: /guides/request-lifecycle
  label: Request lifecycle
---

routeur.ai does not apply a fixed per-key requests-per-minute or tokens-per-minute cap. Throughput is governed by your **plan allowance** at the organisation level, plus **gateway backpressure** when the service is saturated.

## Plan allowance

Each plan includes a pooled monthly routed-request allowance, scaled by seats. Usage beyond the allowance is billed as overage rather than refused — you are not cut off at the boundary.

Traffic is only refused once an organisation passes the blocking guardrail well above its allowance:

:::params
- status: "429"
  code: allowance_exceeded
  desc: The monthly routed-request allowance and its overage guardrail have both been reached. Add seats, upgrade the plan, or contact support to raise the cap.
- status: "402"
  code: billing_inactive
  desc: The organisation's subscription is cancelled. A `past_due` subscription keeps serving traffic through the dunning window.
:::

Both gates are deliberately eventually consistent — usage is rolled up on a schedule, projected to the gateway, and cached — so they take effect within minutes, not instantly.

Read-only metadata endpoints (`GET /v1/models`) deliberately keep working when an organisation is capped, so a client can still connect and discover why it is being refused.

## Budgets

Budgets are separate from plan allowances: you set them per workspace or project in the dashboard to alert on, or stop, spend against your own thresholds. See the dashboard's Cost section.

## Gateway backpressure

Independently of billing, the gateway sheds load when its in-flight request cap is reached:

:::response status=503 label="caller response"
```json
{
  "error": {
    "code":    "overloaded",
    "message": "server is at capacity",
    "type":    "routeur_error"
  }
}
```
:::

A `503 overloaded` carries a `Retry-After` header. Retry with jittered backoff.

## Upstream rate limits

If the upstream provider rate-limits us for your call, that surfaces as `429 upstream_rate_limited`. This reflects the provider's own limits on the credential in use, not a routeur.ai cap.

:::callout amber
**No rate-limit headers.** The gateway does not emit `X-Routeur-RateLimit-*` headers. Track consumption from the usage dashboard or your traces rather than from response headers.
:::
