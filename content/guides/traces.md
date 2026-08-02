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
  "dlp_applied":       true,
  "streamed":          false,
  "gcs_path":          "gs://<project>-routeur-payloads/01K..."
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
  desc: "`default` or `rule:<slug>` depending on how routing resolved."
- name: redactions
  type: integer
  desc: Number of input substitutions DLP made before the upstream call.
- name: dlp_applied / shield_applied / moderation_applied
  type: boolean
  desc: Whether each safety layer ran on this request, regardless of outcome. These drive per-feature metering.
- name: scores
  type: object
  desc: The auto-router's 0–1 capability and safety axes from the single prompt-inspection pass.
- name: streamed
  type: boolean
  desc: True when the response was delivered as SSE.
- name: ttfb_ms
  type: number
  desc: Milliseconds from request start until the first response byte left the gateway. The headline streaming metric.
- name: stream_aborted
  type: boolean
  desc: True when a stream was cut after delivery began — a mid-stream upstream failure or a client disconnect.
- name: disclosed_bytes
  type: integer
  desc: Bytes of model output already delivered when chunked moderation aborted a stream. The audit trail for that trade-off.
- name: gcs_path
  type: string
  note: optional
  desc: Location of the full payload archive, when payload capture is enabled for your organisation.
- name: cost_usd
  type: number
  desc: Calculated estimate from the pricing table for the selected provider/model.
:::

The per-stage timings (`auth_ms`, `dlp_in_ms`, `input_mod_ms`, `routing_ms`,
`upstream_ms`, `moderation_ms`) are also recorded, alongside `client_ip` and
`user_agent`.

## Payload archive

Payload capture is off by default and enabled per organisation. When enabled, routeur.ai writes the full 4-layer payload to archive storage and records its location on the trace as `gcs_path`. The archive bundles every view of the request:

- **caller_request** — raw request body from the application.
- **upstream_request** — what routeur.ai actually sent (post-redaction, post-rule).
- **upstream_response** — raw response from the provider.
- **caller_response** — the response body actually returned to the caller.

:::callout violet
**Audit-friendly.** The four layers together reconstruct any single request — useful for security reviews of what actually crossed the gateway boundary.
:::
