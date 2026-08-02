---
title: Revoke an API key
description: Revoke an API key permanently.
operationId: revokeApiKey
section: API keys
prev:
  label: Create API key
  href: /api-reference/api-keys/create
next:
  label: JavaScript SDK
  href: /sdks/javascript
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage keys in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Revoke an API key. Subsequent requests presenting the revoked key fail with `401 unauthorized` within seconds.

## Returns

Status `204` with an empty body. Revocation is permanent.
