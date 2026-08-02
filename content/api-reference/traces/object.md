---
title: The Trace object
description: A compact record persisted for every request through the gateway.
schema: Trace
section: Traces
prev:
  label: Retrieve model
  href: /api-reference/models/retrieve
next:
  label: List traces
  href: /api-reference/traces/list
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage traces in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

A compact record persisted for every request through the gateway. Traces are read-only — the gateway writes them; the API serves them. See Traces & payloads for the full picture.
