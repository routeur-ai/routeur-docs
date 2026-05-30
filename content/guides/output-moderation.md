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
- name: redact
  desc: Rewrites the matched substring with `[REDACTED]` in the caller-visible content.
- name: rewrite
  desc: Replaces the matched span with a configured substitution string.
- name: block
  note: replaces the response
  desc: Returns `403 blocked_by_moderation` instead of the upstream content. Token usage is still recorded.
:::

## Example: block a managed category

:::code label="rule"
```json
{
  "name":     "secret_leak_block",
  "category": "secret-leak",
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
    "message": "moderation:secret_leak_block",
    "type":    "routeur_error"
  }
}
```
:::

## Managed categories

routeur.ai ships a managed set of categories that map to shared policy detectors:

- **prompt-injection:** response contains an attempt to subvert the system prompt.
- **pii-leak:** response surfaces personally-identifiable information not present in the prompt.
- **secret-leak:** response contains a credential format (API key, private key block).
- **unsafe-code:** response contains a runnable destructive command (e.g. `rm -rf /`).
