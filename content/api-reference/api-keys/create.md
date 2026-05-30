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

Create a new caller API key. The plaintext value of the key is returned exactly once in the response — routeur.ai only stores a hash. If you lose it, revoke and create a new one.

## Returns

Status `201` with the new key metadata *and* the one-time `key` field.
