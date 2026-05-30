---
title: Request lifecycle
description: "The four layers of a routeur.ai request: caller request, upstream request, upstream response, and caller response."
section: Core concepts
prev:
  href: /guides/quickstart
  label: Quickstart
next:
  href: /guides/routing-rules
  label: Routing rules
---

When a request goes through routeur.ai there are four distinct views worth documenting: what the caller sent, what routeur.ai passed upstream, what the model returned, and what routeur.ai finally returned to the caller. Every page of the API reference describes a single hop in this chain — this page is the map.

## Four layers

Each numbered box below is its own JSON document. Together they form the complete picture of any single request.

:::cards cols=2
- label: 1 · Caller → routeur.ai
  desc: The original OpenAI-compatible request body from your application.
- label: 2 · Routeur → LLM
  desc: The upstream request after routing, DLP, and any one-request overrides have been applied.
- label: 3 · LLM → routeur.ai
  desc: The raw upstream response from the provider adapter.
- label: 4 · Routeur → caller
  desc: The OpenAI-compatible response or short JSON error the caller actually receives.
:::

## Example: input DLP redaction

This is the most important example for security reviews because it proves the LLM never sees the original sensitive value.

:::codegrid cols=2 label1="1 · Caller request" label2="2 · Upstream request" label3="3 · Upstream response" label4="4 · Caller response"
```json
{
  "model": "auto",
  "messages": [{
    "role": "user",
    "content": "Repeat this card: 4111 1111 1111 1111"
  }]
}
```
```json
{
  "model": "gpt-4o-mini",
  "messages": [{
    "role": "user",
    "content": "Repeat this card: [REDACTED]"
  }]
}
```
```json
{
  "id": "chatcmpl_...",
  "model": "gpt-4o-mini-2024-07-18",
  "choices": [{
    "message": {
      "role": "assistant",
      "content": "I can't assist with that."
    },
    "finish_reason": "stop"
  }]
}
```
```json
{
  "id": "chatcmpl_...",
  "model": "gpt-4o-mini-2024-07-18",
  "choices": [ ... ],
  "usage": { ... },
  "routeur": {
    "provider": "openai",
    "model": "gpt-4o-mini",
    "route_reason": "default",
    "redactions": 1
  }
}
```
:::

:::callout violet
**Security property.** The caller sent the real card number, but the upstream provider only received `[REDACTED]`.
:::

## Example: output moderation block

Output moderation is the inverse case: the upstream model does produce content, but routeur.ai prevents that content from reaching the caller.

:::response status=400 label="Caller response"
```json
{
  "error": {
    "code": "blocked_by_moderation",
    "message": "moderation:secret_leak_block",
    "type": "routeur_error"
  }
}
```
:::

:::callout amber
**Important distinction.** With output moderation, the LLM has already seen the prompt and answered. The control protects the end user, not the upstream model.
:::

## Trace records and payload archives

Routeur exposes two observability layers for audits and debugging.

- **Trace record:** compact request metadata persisted for every request, including provider, requested model, final model, latency, token counts, cost, and an optional `payload_url`.
- **Payload archive:** the full request and response bundle, encrypted at rest and fetched via a short-lived signed URL. The archive includes the caller request, upstream request, upstream response, and the response body returned to the caller.

:::code label="Trace record"
```json
{
  "request_id": "01K...",
  "organization_id": "org_42",
  "provider": "openai",
  "model": "gpt-4o-mini",
  "requested_model": "auto",
  "route_reason": "default",
  "latency_ms": 2939,
  "prompt_tokens": 13,
  "completion_tokens": 7,
  "cost_usd": 0.00000615,
  "payload_url": "https://payloads.routeur.ai/...?sig=..."
}
```
:::
