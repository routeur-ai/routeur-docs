---
title: Retrieve a route
description: Fetch a single routing rule by its id.
operationId: retrieveRoute
section: Routes
prev:
  label: Create route
  href: /api-reference/routes/create
next:
  label: Replace route
  href: /api-reference/routes/replace
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage routing rules in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Fetch a single routing rule by its id.

## Returns

The matching [Route object](/api-reference/routes/object), or `404 not_found`.
