---
title: Create a chat completion
description: Send a chat request through routeur.ai. OpenAI-compatible.
operationId: createChatCompletion
section: Chat completions
prev:
  label: Authentication
  href: /guides/authentication
next:
  label: Routes
  href: /api-reference/routes/list
---

Send a chat request through routeur.ai. The request body is OpenAI-compatible: routing, DLP, and moderation are applied transparently by the gateway before the upstream provider sees the prompt.

## Returns

An OpenAI-compatible chat completion object. With `Routeur-Trace: true`, the response also includes a `routeur` block describing the route and any redactions applied.

## Streaming

Set `"stream": true` to receive the response as server-sent events. The
gateway emits `Content-Type: text/event-stream` and a sequence of
`chat.completion.chunk` objects — one per `data:` line — terminated by a final
`data: [DONE]` sentinel. The format matches OpenAI's, so any OpenAI-compatible
client (the `openai` SDKs, the Vercel AI SDK, LangChain) consumes it with no
special-casing.

```bash
curl https://api.routeur.ai/v1/chat/completions \
  -H "Authorization: Bearer $ROUTEUR_KEY" \
  -H "Content-Type: application/json" \
  -N \
  -d '{"model":"auto","stream":true,"messages":[{"role":"user","content":"Hello"}]}'
```

- **Token deltas** arrive in `choices[].delta.content`.
- **Usage** is reported on the final chunk before `[DONE]` (`prompt_tokens`,
  `completion_tokens`, `total_tokens`) — routeur always includes it.
- **Trace metadata.** With `Routeur-Trace: true`, the `routeur` block is
  delivered as one extra chunk (empty `choices`) immediately before `[DONE]`.

### Moderated organizations buffer first

If your organization has **output-moderation** rules, a streamed request is
held until the full upstream response has been generated and moderated, then
replayed to you as a stream. You still receive a spec-compliant event stream;
it simply begins after moderation completes rather than token-by-token. This
preserves the guarantee that blocked content never reaches the caller. See
[Output moderation](/guides/output-moderation) for the `buffered` vs `chunked`
modes.

### Errors mid-stream

Once the first chunk has been sent the HTTP status is committed to `200`, so a
failure after that point is delivered as a terminal SSE event rather than an
HTTP error:

```
event: error
data: {"error":{"code":"upstream_error","message":"...","type":"routeur_error"},"request_id":"01K..."}
```

A pre-stream failure (auth, routing, an upstream that never produced a byte)
is returned as the usual JSON error with the appropriate status code. See
[Errors](/guides/errors) for the full list.
