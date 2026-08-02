---
title: Replace a route
description: Replace the entire rule definition.
operationId: replaceRoute
section: Routes
prev:
  label: Retrieve route
  href: /api-reference/routes/retrieve
next:
  label: Update route
  href: /api-reference/routes/update
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage routing rules in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Replace the entire rule definition. Any field omitted from the body is reset to its default. Use Update route for partial changes.

## Returns

The replaced [Route object](/api-reference/routes/object) with a fresh `updated_at`.
