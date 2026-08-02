---
title: Create a route
description: Add a new routing rule.
operationId: createRoute
section: Routes
prev:
  label: List routes
  href: /api-reference/routes/list
next:
  label: Retrieve route
  href: /api-reference/routes/retrieve
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage routing rules in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Add a new routing rule. The rule is active for new requests within a few seconds — the gateway re-reads rules at a short polling interval.

## Returns

The newly created [Route object](/api-reference/routes/object).
