---
title: Authentication
description: How routeur.ai API keys work and how to send them with every request.
section: Get started
prev:
  href: /guides/quickstart
  label: Quickstart
next:
  href: /guides/errors
  label: Errors
---

Routeur uses bearer tokens. Every request to `api.routeur.ai` must include an `Authorization` header carrying a routeur.ai API key. Upstream provider keys never leave the routeur.ai control plane — your application only needs the routeur.ai key.

## Header format

:::code label="request headers"
```http
Authorization: Bearer rtr_your_routeur_api_key
Content-Type:  application/json
```
:::

## Key format

Routeur keys are prefixed with `rtr_` so they're easy to spot in logs and secret scanners. The plaintext value is shown once at creation time; routeur.ai stores only a one-way hash.

## Loading the key

Treat the routeur.ai key as an environment-level secret. Never commit it. The standard names used in the SDK examples are:

- `ROUTEUR_KEY` — the recommended variable name.
- `OPENAI_API_KEY` — works when reusing the OpenAI SDK with no code changes, as long as you also override the base URL.

## Auth errors

:::params
- status: "401"
  code: unauthorized
  note: missing or unknown key
  desc: The `Authorization` header is absent, malformed, or carries a key routeur.ai does not recognise.
- status: "403"
  code: forbidden
  note: revoked or disabled key
  desc: The key exists but has been revoked or its owning organisation is disabled.
:::

## Rotating keys

Create a replacement key before revoking the current one. Roll the new value through deployment, then revoke the old key from the dashboard. Revocation takes effect within seconds because the gateway checks key validity on every request.
