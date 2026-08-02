---
title: Output moderation
description: Output moderation runs on the upstream model's response and can block, redact, or rewrite it before the caller sees it.
section: Core concepts
prev:
  href: /guides/dlp
  label: DLP
next:
  href: /guides/traces
  label: Traces & payloads
---

Output moderation runs *after* the upstream model has answered but *before* the response is returned to the caller. The LLM does see the prompt; routeur.ai protects the end user from what the model produced.

:::callout amber
**Different from DLP.** DLP protects the upstream model from your input. Output moderation protects your users from the model's output. They run at different points in the request lifecycle.
:::

## Actions

:::params
- name: log
  note: non-blocking
  desc: Records the match on the trace without modifying the response.
- name: block
  note: replaces the response
  desc: Returns `403 blocked_by_moderation` instead of the upstream content. Token usage is still recorded.
:::

:::callout amber
**`redact` and `rewrite` are not enforced on output yet.** The schema accepts
them and a match is still recorded on the trace, but the response is returned
unmodified. Use `block` if the content must not reach the caller. Input-side
redaction *is* enforced — see [DLP](/guides/dlp).
:::

## Example: block a managed category

:::code label="rule"
```json
{
  "name":     "hate_block",
  "category": "hate",
  "action":   "block",
  "severity": "high",
  "enabled":  true
}
```
:::

:::response status=403 label="caller response"
```json
{
  "error": {
    "code":    "blocked_by_moderation",
    "message": "moderation:hate_block",
    "type":    "routeur_error"
  }
}
```
:::

## Managed categories

routeur.ai ships eight managed output categories, matched in-process against
curated word-lists. See [presets](/guides/presets) for the full table.

| Category | Severity |
|---|---|
| `hate` | high |
| `sexual` | high |
| `sexual_minors` | high |
| `self_harm` | high |
| `violence` | high |
| `harassment` | medium |
| `profanity` | medium |
| `weapons` | medium |

Set `category` to `regex` instead to supply your own `pattern`.

Prompt-injection detection is a separate feature — see
[presets](/guides/presets) for the prompt-shield categories. Input-side PII and
secret detection is [DLP](/guides/dlp).

## Streaming behaviour

When a caller requests a streamed response (`"stream": true`), output
moderation has to choose between protecting the caller and delivering tokens as
they arrive. Two modes control this, set per organization with an optional
per-rule override.

:::params
- mode: buffered
  desc: The gateway drains the full upstream response, runs moderation, and only then replays it to the caller as an SSE stream. Blocked content never reaches the caller; the stream simply starts after moderation completes. This is the default.
- mode: chunked
  desc: The gateway forwards chunks as they arrive and scans incrementally, aborting the stream with a terminal error event on a block. Lower time-to-first-token, but some content may reach the caller before the verdict is known.
:::

**Resolution order.** A rule's own `streaming` setting wins over the org-wide
default. If *any* applicable rule resolves to `buffered`, the whole request is
buffered — a single conservative rule cannot be bypassed by an org-level
`chunked` default. Only when every applicable rule resolves to `chunked` is the
response streamed with chunked moderation.

:::callout amber
**`chunked` is opt-in for a reason.** In chunked mode the gateway forwards
tokens as they arrive and scans the accumulated output as it goes. When a rule
matches, the gateway stops — it never forwards the chunk that completed the
violation — but tokens sent before that point have already reached the caller.
Choose `chunked` only where that disclosure is acceptable; leave the default
`buffered` when it is not.
:::

### How a chunked block is delivered

Because the stream's HTTP status is already committed to `200`, a chunked block
arrives as a terminal SSE error event, after which the stream closes (no
`[DONE]`):

```
event: error
data: {"error":{"code":"blocked_by_moderation","message":"moderation:<rule>","type":"routeur_error"},"request_id":"01K..."}
```

If the very first chunk trips the rule — nothing has been sent yet — the caller
instead gets the ordinary buffered `403` JSON, identical to a non-streamed
block.

Every chunked block is audited: the trace records `block_stage:
output_moderation`, the matching rule, `stream_aborted: true`, and
`disclosed_bytes` — exactly how many bytes of model output reached the caller
before the stream was cut. The payload archive still holds the full upstream
response (including the violating content that was *not* forwarded), so an
auditor can see both what the model produced and what the caller saw.

### When `chunked` falls back to `buffered`

Incremental scanning needs a bounded window: the gateway keeps a sliding window
of recent bytes so a match that straddles a chunk boundary is still found. A
rule whose detector could match more than that window (a deliberately
unbounded pattern) cannot be scanned safely in chunks, so a request that would
otherwise be `chunked` is served as `buffered` instead — correctness is never
traded for latency.
