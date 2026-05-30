---
title: Traces & payloads
description: Trace records and payload archives — how routeur.ai exposes observability for audits and debugging.
section: Core concepts
prev:
  href: /guides/output-moderation
  label: Output moderation
next:
  href: /guides/presets
  label: Presets reference
---

Every request produces two artefacts: a small **trace record** (always) and, when payload storage is enabled, a full **payload archive** with the original caller request, upstream request, upstream response, and the final caller response.

## Trace record

A compact metadata record, persisted for every request and returned inline when the caller sends `Routeur-Trace: true`.

:::code label="trace record"
```json
{
  "request_id":        "01K...",
  "organization_id":   "org_42",
  "api_key_id":        "key_01J...",
  "provider":          "openai",
  "model":             "gpt-4o-mini",
  "requested_model":   "auto",
  "route_reason":      "default",
  "latency_ms":        2939,
  "prompt_tokens":     13,
  "completion_tokens": 7,
  "total_tokens":      20,
  "cost_usd":          0.00000615,
  "status_code":       200,
  "redactions":        1,
  "payload_url":       "https://payloads.routeur.ai/...?sig=..."
}
```
:::

## Trace fields

:::params
- name: request_id
  type: string
  desc: Stable id used across response metadata, trace records, and payload archives.
- name: route_reason
  type: string
  desc: "`default` or `rule:<name>` depending on how routing resolved."
- name: redactions
  type: integer
  desc: Number of input substitutions DLP made before the upstream call.
- name: payload_url
  type: string
  note: optional
  desc: Pre-signed URL to the full payload archive when payload storage is enabled. Short-lived; refetch the trace to mint a new one.
- name: cost_usd
  type: number
  desc: Calculated estimate from the pricing table for the selected provider/model.
:::

## Payload archive

When enabled, routeur.ai writes the full 4-layer payload to encrypted archive storage and exposes a short-lived signed URL via `payload_url`. The archive bundles every view of the request:

- **caller_request** — raw request body from the application.
- **upstream_request** — what routeur.ai actually sent (post-redaction, post-rule).
- **upstream_response** — raw response from the provider.
- **caller_response** — the response body actually returned to the caller.

:::callout violet
**Audit-friendly.** The four layers together reconstruct any single request — useful for security reviews of what actually crossed the gateway boundary.
:::
