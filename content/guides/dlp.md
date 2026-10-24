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

Input data-loss prevention (DLP) runs on the caller's prompt *before* routeur.ai forwards the request to the upstream provider. Each policy either logs a match, alerts the caller, rewrites the match in place (Tokenize or Redact) or stops the request (Block). With Tokenize, Redact or Block, the upstream model never sees the original sensitive value.

## Actions

Each policy has one action. The app shows a label; the stored value (used in the API and in trace records) is in backticks.

:::params
- name: Log (`log`)
  note: non-blocking
  desc: "Records detections for monitoring and forwards the request unchanged. Detections appear on the DLP activity page. Useful while tuning a new policy."
- name: Alert (`warn`)
  note: tells the caller
  desc: "Forwards the request unchanged and adds a `Routeur-Policy-Warning` response header naming the matching policies, so the calling application can react. See the example below. Matched text is never included."
- name: Tokenize (`tokenize`)
  note: Pro and above · reversible
  desc: "Replaces each match with a numbered token such as `[EMAIL_1]` before the provider call, then restores the original values in the response. See the tokenization example below."
- name: Redact (`redact`)
  note: rewrites in place
  desc: "Replaces the match with `[REDACTED:<ID>]` for presets and secrets, such as `[REDACTED:CREDIT_CARD]`, or with `[REDACTED]` for a custom pattern. When a policy selects several secret formats the replacement is `[REDACTED:SECRET]`. The number of substitutions is reported as `routeur.redactions` in the trace metadata block."
- name: Block (`block`)
  note: stops the request
  desc: "Returns `403` with code `blocked_by_dlp` and the policy name in the message. No upstream call is made."
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

## Example: warn the calling application

A `warn` rule never changes or stops the request. When it matches, the response carries a `Routeur-Policy-Warning` header listing the slugs of the matching policies, comma-separated and de-duplicated. The header is present on every response to that request, including streamed responses and requests that are blocked or fail later in the pipeline. It is absent when no `warn` rule matched.

:::code label="response headers"
```http
HTTP/1.1 200 OK
Content-Type: application/json
Routeur-Policy-Warning: dlp; policies="project_code_warn,confidential_warn"
```
:::

With `Routeur-Trace: true`, the same slugs are returned as `routeur.policy_warnings` in the trace metadata block.

## Example: tokenize and restore

Redact is one-way: the model sees `[REDACTED:EMAIL]` and so does the caller's application if the model repeats it. Tokenize is reversible. Each distinct match becomes a numbered token before the provider call, and the original values are put back in the response, so the model can reason about "the person at `[EMAIL_1]`" without ever seeing the address.

:::codegrid cols=2 label1="caller sends" label2="upstream sees"
```text
Draft a reply to alice@example.com and
copy in bob@example.com.
```
```text
Draft a reply to [EMAIL_1] and
copy in [EMAIL_2].
```
:::

:::codegrid cols=2 label1="upstream replies" label2="caller receives"
```text
Dear [EMAIL_1], thanks for your note.
Cc: [EMAIL_2]
```
```text
Dear alice@example.com, thanks for your note.
Cc: bob@example.com
```
:::

What to know about Tokenize:

- **Restoration is thorough.** Tokens are restored in message text, in tool-call arguments, and in streamed responses, including a token split across two chunks.
- **The token map is never stored.** It exists in memory only for the duration of that one request. It is not written to traces, logs or any database.
- **Mangled tokens are not restored.** If the model rewrites a token (for example `[email_1]` or `EMAIL 1`), it reaches the caller as written.
- **Embeddings behave like Redact.** There is no response text to restore, so matches are replaced with the redaction marker instead.
- **Trace counters.** With `Routeur-Trace: true`, the `routeur` trace block gains `tokens_issued` and `tokens_restored`. A gap between the two means the model dropped or altered some tokens.
- **Secrets are never restored.** A match from a Secrets policy (or a legacy secret preset such as `aws_key`) becomes a token like `[SECRET_1]`, but routeur.ai never keeps the original value, so it can't come back in a response. This stops a prompt injection from steering a credential into a tool call or link.
- **Restored data can still be steered.** Personal data is restored wherever the model writes its token, including tool-call arguments. If your application acts on tool calls or renders links from untrusted content, treat restored values as you would the original input.
- **JSON output stays valid.** With a JSON `response_format`, restored values are escaped as JSON strings. If you ask for JSON only in the prompt, without setting `response_format`, values are restored as raw text, so set `response_format` whenever you parse the reply as JSON.
- **Your own secret formats.** Only Secrets policies (and the legacy secret presets) are never restored. A Regular expression or Keywords policy you write for your own credential format is restored like any other match. Use Redact for those, or select a matching format under Secrets.
- **Plan.** Tokenize is available on Pro and above. Other plans can use Log, Alert, Redact or Block.

## Detection types

When you create a policy you choose what it detects.

- **Regular expression:** a pattern you supply, matched against message content. See [Regular expression syntax](#regular-expression-syntax-re2).
- **Presets:** managed detectors maintained by routeur.ai for common sensitive-data categories (email, phone, credit card, IBAN, national identifiers, JWTs, PEM blocks, and more). Many use checksums and context words to cut false positives. See [Presets reference · DLP](/guides/presets#dlp) for the full catalogue and examples.
- **Secrets:** API keys, tokens and private keys for 219 formats. Select only the providers you use. See [Secret formats](/guides/secret-formats).
- **Keywords:** literal substring match, case-insensitive.

In the rule JSON shown in the examples above, these are the `regex`, `preset`, `secret` and `keyword` values of `detector`.

## Regular expression syntax (RE2)

Patterns use [RE2](https://github.com/google/re2/wiki/Syntax) syntax, the same engine the gateway runs, so a pattern behaves identically when you test it and when it is enforced. RE2 matches in linear time, which keeps a pattern from slowing a request.

RE2 does not support lookahead or lookbehind, backreferences (such as `\1`), or possessive quantifiers (such as `a++`). A pattern that uses them is rejected when you save the policy, with a message explaining why.

## When a pattern cannot be compiled

DLP fails closed. If a policy's pattern cannot be compiled, for example because it was saved before RE2 validation existed, routeur.ai does not quietly let matching traffic through. Requests in that policy's scope receive `503` with code `policy_invalid`, and the message names the policy so you can find and fix it.

:::response status=503 label="caller response"
```json
{
  "error": {
    "code":    "policy_invalid",
    "message": "dlp policy \"project_code_redact\" has a pattern the gateway cannot compile; fix it in the admin panel",
    "type":    "routeur_error"
  }
}
```
:::

The exception is a Log or Alert policy. It does not change or stop traffic, so a policy with an invalid pattern is skipped instead and requests carry on. See [Errors](/guides/errors) for all error codes.
