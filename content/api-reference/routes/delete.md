---
title: Delete a route
description: Permanently delete a routing rule.
operationId: deleteRoute
section: Routes
prev:
  label: Update route
  href: /api-reference/routes/update
next:
  label: The Model object
  href: /api-reference/models/object
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage routing rules in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Permanently delete a routing rule. The change takes effect for new requests once it has propagated to the gateway — allow up to about a minute. Prefer disabling the rule if you want a reversible change.

## Returns

An empty body with status `204`.
