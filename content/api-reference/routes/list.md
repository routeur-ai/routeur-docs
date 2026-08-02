---
title: List routes
description: Return every routing rule belonging to the authenticated organisation.
operationId: listRoutes
section: Routes
prev:
  label: The Route object
  href: /api-reference/routes/object
next:
  label: Create route
  href: /api-reference/routes/create
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage routing rules in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Return every routing rule belonging to the authenticated organisation, sorted by `priority` descending.

## Returns

An object with `data` (array of [Route objects](/api-reference/routes/object)) and `has_more` (boolean).
