---
title: List traces
description: Return trace records for the authenticated organisation in reverse chronological order.
operationId: listTraces
section: Traces
prev:
  label: The Trace object
  href: /api-reference/traces/object
next:
  label: Retrieve trace
  href: /api-reference/traces/retrieve
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage traces in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Return trace records for the authenticated organisation in reverse chronological order. Use the cursor-style `starting_after` parameter to paginate.

## Returns

An object with `data` (array of [Trace objects](/api-reference/traces/object)) and `has_more`.
