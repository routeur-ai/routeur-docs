---
title: DLP
description: Input DLP redacts or blocks sensitive substrings before they reach the upstream LLM.
section: Core concepts
prev:
  href: /guides/routing-rules
  label: Routing rules
next:
  href: /guides/output-moderation
  label: Output moderation
---

Input data-loss prevention (DLP) runs on the caller's prompt *before* routeur.ai forwards the request to the upstream provider. Each rule either rewrites a match in place (`redact`) or stops the request (`block`). The upstream model never sees the original sensitive value.

## Actions

:::params
- name: log
  note: non-blocking
  desc: Records a match in the trace without modifying the request. Useful while tuning a new rule.
- name: redact
  note: rewrites in place
  desc: Replaces the matched substring with `[REDACTED]` before the upstream call. The number of substitutions is reported in `routeur.redactions` when you send `Routeur-Trace: true`.
- name: warn
  note: trace flag
  desc: "Like `log` but raises severity on the trace record."
- name: block
  note: stops the request
  desc: Returns `403 blocked_by_dlp` with the rule name in the message. No upstream call is made.
:::

## Example: redact an internal identifier

Bring-your-own pattern. Anything matching the rule is rewritten before the upstream call sees it.

:::codegrid cols=2 label1="rule" label2="upstream sees"
```json
{
  "name":     "project_code_redact",
  "detector": "regex",
  "pattern":  "PROJ-[A-Z0-9]{6}",
  "action":   "redact",
  "severity": "medium",
  "enabled":  true
}
```
```json
{
  "model": "gpt-4o-mini",
  "messages": [{
    "role": "user",
    "content": "Summarise ticket [REDACTED] for the standup."
  }]
}
```
:::

## Example: block a confidentiality marker

:::code label="rule"
```json
{
  "name":     "confidential_block",
  "detector": "keyword",
  "pattern":  "INTERNAL-ONLY",
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
    "code":    "blocked_by_dlp",
    "message": "dlp:confidential_block",
    "type":    "routeur_error"
  }
}
```
:::

## Detector types

- **regex:** caller-supplied regular expression matched against message content.
- **keyword:** literal substring match, case-insensitive.
- **preset:** managed detectors maintained by routeur.ai for common sensitive-data categories (email, phone, credit card, IBAN, JWTs, AWS keys, PEM blocks, …). See [Presets reference · DLP](/guides/presets#dlp) for the full catalogue and examples.
