---
title: Create an API key
description: Create a new caller API key.
operationId: createApiKey
section: API keys
prev:
  label: List API keys
  href: /api-reference/api-keys/list
next:
  label: Revoke API key
  href: /api-reference/api-keys/revoke
---

:::callout amber
**Not available yet.** The management API is documented ahead of its implementation — the request and response shapes below are a stable contract we're committing to, but calling these endpoints today returns `404`. Manage keys in the [dashboard](https://app.routeur.ai) in the meantime. The live gateway endpoints today are `POST /v1/chat/completions`, `POST /v1/embeddings` and `GET /v1/models`.
:::

Create a new caller API key. The plaintext value of the key is returned exactly once in the response — routeur.ai only stores a hash. If you lose it, revoke and create a new one.

## Returns

Status `201` with the new key metadata *and* the one-time `key` field.
