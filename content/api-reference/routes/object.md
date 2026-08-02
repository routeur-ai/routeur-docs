---
title: The Route object
description: A routing rule that maps a caller request to a concrete provider/model pair.
schema: Route
section: Routes
prev:
  label: Create chat completion
  href: /api-reference/chat-completions/create
next:
  label: List routes
  href: /api-reference/routes/list
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage routing rules in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

A routing rule that maps a caller request to a concrete `(provider, model)` pair. Rules are evaluated highest `priority` first; the first matching rule wins.
