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
- label: 2 · routeur.ai → LLM
  desc: The upstream request after routing, DLP, and any one-request overrides have been applied.
- label: 3 · LLM → routeur.ai
  desc: The raw upstream response from the provider adapter.
- label: 4 · routeur.ai → caller
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
    "content": "Repeat this card: [REDACTED:CREDIT_CARD]"
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
**Security property.** The caller sent the real card number, but the upstream provider only received `[REDACTED:CREDIT_CARD]`.
:::

The other DLP actions change this flow differently. Log and Alert forward the request unchanged, Tokenize swaps the match for a token and restores it in step 4, and Block stops the request after step 1 with `403 blocked_by_dlp`. If a policy's pattern cannot be compiled, the request stops at the same point with `503 policy_invalid`. See [DLP](/guides/dlp).

## Example: output moderation block

Output moderation is the inverse case: the upstream model does produce content, but routeur.ai prevents that content from reaching the caller.

:::response status=403 label="Caller response"
```json
{
  "error": {
    "code": "blocked_by_moderation",
    "message": "moderation:hate_block",
    "type": "routeur_error"
  }
}
```
:::

:::callout amber
**Important distinction.** With output moderation, the LLM has already seen the prompt and answered. The control protects the end user, not the upstream model.
:::

## Streaming

The same four layers apply to streamed requests (`"stream": true`). The
caller request and upstream request are identical; layers 3 and 4 are simply
delivered incrementally as `chat.completion.chunk` events rather than a single
JSON body. For an organization with **output-moderation** rules in the default
`buffered` mode the gateway buffers the full upstream response, moderates it,
and only then replays it as a stream — so the moderation security property
above is preserved unchanged.

The one exception is the opt-in `chunked` mode: there the gateway forwards
tokens as it scans, so a caller may receive some content before a block fires.
The "caller never sees blocked content" guarantee therefore holds for buffered
moderation (and for every non-streamed request) and is explicitly waived, per
org or per rule, only by choosing `chunked`. See
[Create a chat completion → Streaming](/api-reference/chat-completions/create)
for the wire format and [Output moderation](/guides/output-moderation) for the
`buffered` vs `chunked` modes.

## Trace records and payload archives

routeur.ai exposes two observability layers for audits and debugging.

- **Trace record:** compact request metadata persisted for every request, including provider, requested model, final model, latency, token counts, cost, and — when payload capture is enabled — the archive's `gcs_path`.
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
  "gcs_path": "gs://<project>-routeur-payloads/01K..."
}
```
:::
