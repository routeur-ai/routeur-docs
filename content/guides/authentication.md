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

routeur.ai uses bearer tokens. Every request to `api.routeur.ai` must include an `Authorization` header carrying a routeur.ai API key. Upstream provider keys never leave the routeur.ai control plane — your application only needs the routeur.ai key.

## Header format

:::code label="request headers"
```http
Authorization: Bearer sk-r-your_routeur_api_key
Content-Type:  application/json
```
:::

## Key format

routeur.ai keys are prefixed with `sk-r-` so they're easy to spot in logs and secret scanners. The plaintext value is shown once at creation time; routeur.ai stores only a one-way hash.

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
- status: "402"
  code: billing_inactive
  note: no active subscription
  desc: The key is valid but the owning organisation has no active subscription.
:::

A revoked or disabled key returns `401 unauthorized`, the same as an unknown
key — the gateway does not distinguish the two to the caller.

## Rotating keys

Create a replacement key before revoking the current one. Roll the new value through deployment, then revoke the old key from the dashboard. Allow up to about a minute for the revocation to take effect across the gateway — the gateway checks key validity on every request, but against configuration that propagates from the dashboard asynchronously.
