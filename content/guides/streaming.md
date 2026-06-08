---
title: Streaming
description: "Consume routeur.ai responses as server-sent events: chunk anatomy, tool-call deltas, usage, errors, and cancellation."
section: Core concepts
prev:
  href: /guides/request-lifecycle
  label: Request lifecycle
next:
  href: /guides/routing-rules
  label: Routing rules
---

Set `"stream": true` to receive a response as it is generated. routeur.ai emits
an OpenAI-compatible server-sent event stream, so any OpenAI client library —
the `openai` SDKs, the Vercel AI SDK, LangChain — consumes it with no
special-casing. Point the client at `https://api.routeur.ai/v1` and stream as
usual.

## The wire format

The response is `Content-Type: text/event-stream`: a sequence of
`chat.completion.chunk` objects, one per `data:` line, terminated by a literal
`data: [DONE]`.

```
data: {"id":"chatcmpl_...","object":"chat.completion.chunk","choices":[{"index":0,"delta":{"role":"assistant"}}]}

data: {"object":"chat.completion.chunk","choices":[{"index":0,"delta":{"content":"Hello"}}]}

data: {"object":"chat.completion.chunk","choices":[{"index":0,"delta":{},"finish_reason":"stop"}],"usage":{"prompt_tokens":9,"completion_tokens":2,"total_tokens":11}}

data: [DONE]
```

- **Token text** arrives in `choices[].delta.content`.
- **The terminal chunk** (before `[DONE]`) carries `finish_reason` and `usage`.
- **`usage`** is included by default. Send `"stream_options": {"include_usage":
  false}` to suppress it on the wire (routeur still meters internally for
  billing and traces).
- With `Routeur-Trace: true`, the `routeur` meta block (provider, model,
  route reason, latency, cost) is delivered as one extra chunk with empty
  `choices` immediately before `[DONE]`.

`n > 1` is rejected with `stream: true` (`invalid_request`) — stream a single
choice.

## Tool calls

Tool calls stream as incremental `delta.tool_calls` fragments in the same
OpenAI shape, regardless of which upstream answered (the Anthropic and Gemini
adapters translate their native tool events). Reassemble by `index`,
concatenating `function.arguments`:

```
data: {"choices":[{"index":0,"delta":{"tool_calls":[{"index":0,"id":"call_1","type":"function","function":{"name":"get_weather","arguments":""}}]}}]}

data: {"choices":[{"index":0,"delta":{"tool_calls":[{"index":0,"function":{"arguments":"{\"city\":"}}]}}]}

data: {"choices":[{"index":0,"delta":{"tool_calls":[{"index":0,"function":{"arguments":"\"paris\"}"}}]}}]}

data: {"choices":[{"index":0,"delta":{},"finish_reason":"tool_calls"}]}
```

Tool-call arguments are model output, so output moderation scans them just like
content.

## Errors and cancellation

Once the first chunk is sent the HTTP status is committed to `200`. A failure
after that point is a terminal `event: error` frame, after which the stream
closes (no `[DONE]`):

```
event: error
data: {"error":{"code":"upstream_error","message":"...","type":"routeur_error"},"request_id":"01K..."}
```

A failure *before* the first chunk (auth, routing, an output-moderation block on
an unmoderated-stream org) is the ordinary JSON error with its normal status —
no stream is opened. See [Errors](/guides/errors) for codes and retry guidance.

**Cancelling a stream.** To stop a response early — a chat UI's "stop" button —
just abort the HTTP request (close the connection, or cancel the fetch /
`AbortController`). routeur.ai detects the disconnect and **cancels the upstream
call**, so you are not billed for tokens generated after you stopped. No
special API call is needed.

```js
const controller = new AbortController();
const res = await fetch("https://api.routeur.ai/v1/chat/completions", {
  method: "POST",
  headers: { Authorization: `Bearer ${KEY}`, "Content-Type": "application/json" },
  body: JSON.stringify({ model: "auto", stream: true, messages }),
  signal: controller.signal,
});
// …later, when the user hits "stop":
controller.abort();
```

**Truncated streams.** A deploy or autoscale event can cut a long-lived stream
(no terminal `error`, no `[DONE]`). This is expected operationally — treat it
like a retryable error and re-issue the request idempotently.

## Output moderation while streaming

How output moderation applies to a stream is configurable per organization (and
per rule). In the default **buffered** mode the gateway scans the whole response
before sending, so a streamed request from a moderated org simply starts after
moderation completes. In opt-in **chunked** mode tokens stream as they are
scanned and a block aborts the stream with the `blocked_by_moderation` terminal
event. See [Output moderation → Streaming behaviour](/guides/output-moderation).
