---
title: Retrieve a trace
description: Fetch a single trace record by request_id.
operationId: retrieveTrace
section: Traces
prev:
  label: List traces
  href: /api-reference/traces/list
next:
  label: Inspect trace
  href: /api-reference/traces/inspect
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage traces in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Fetch a single trace record by `request_id`. When the trace has a `payload_url`, follow it to download the full payload archive — the URL is short-lived and signed; refetch the trace to mint a fresh one.

## Returns

The matching [Trace object](/api-reference/traces/object), or `404 not_found`.
