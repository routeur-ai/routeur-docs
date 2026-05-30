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

Return metadata for every caller API key on the organisation. Plaintext key material is never included — only a short, non-secret `prefix` so you can identify each key.

## Returns

An object with `data`: an array of key metadata. Each entry includes `id`, `name`, `prefix`, `created_at`, and `last_used_at`.
