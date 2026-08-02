---
title: List API keys
description: Return metadata for every caller API key on the organisation.
operationId: listApiKeys
section: API keys
prev:
  label: Inspect trace
  href: /api-reference/traces/inspect
next:
  label: Create API key
  href: /api-reference/api-keys/create
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage keys in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Return metadata for every caller API key on the organisation. Plaintext key material is never included — only a short, non-secret `prefix` so you can identify each key.

## Returns

An object with `data`: an array of key metadata. Each entry includes `id`, `name`, `prefix`, `created_at`, and `last_used_at`.
