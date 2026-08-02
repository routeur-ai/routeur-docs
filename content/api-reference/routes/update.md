---
title: Update a route
description: Partially update a routing rule.
operationId: updateRoute
section: Routes
prev:
  label: Replace route
  href: /api-reference/routes/replace
next:
  label: Delete route
  href: /api-reference/routes/delete
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage routing rules in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Partially update a routing rule. Only the fields you supply are changed; everything else is left as-is. Common use: flip `enabled` or adjust `priority`.

## Returns

The updated [Route object](/api-reference/routes/object).
