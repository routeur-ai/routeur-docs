---
title: Inspect a trace
description: HEAD request returning trace metadata as response headers.
operationId: inspectTrace
section: Traces
prev:
  label: Retrieve trace
  href: /api-reference/traces/retrieve
next:
  label: List API keys
  href: /api-reference/api-keys/list
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage traces in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

A lightweight HEAD request that returns metadata about a trace as response headers, without transferring the trace body. Useful for checking whether a payload archive exists before you fetch it.

## Returns

An empty body. Status is `200` when the trace exists, `404` otherwise.
