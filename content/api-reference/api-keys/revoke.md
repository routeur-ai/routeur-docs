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

Revoke an API key. Subsequent requests presenting the revoked key fail with `401 invalid_api_key` within seconds.

## Returns

Status `204` with an empty body. Revocation is permanent.
