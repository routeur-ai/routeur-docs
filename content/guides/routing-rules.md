---
title: Routing rules
description: How routing rules pick the upstream provider and model for each request.
section: Core concepts
prev:
  href: /guides/request-lifecycle
  label: Request lifecycle
next:
  href: /guides/dlp
  label: DLP
---

A routing rule maps a caller request to a concrete `(provider, model)` pair. Rules are sorted by priority and evaluated in order; the first matching rule wins. If no rule matches, routeur.ai falls back to its built-in default for the requested alias.

## Evaluation order

1. Filter to enabled rules.
2. Sort by `priority` descending.
3. Evaluate `match` against the request. First hit wins.
4. Apply the rule's `action`; record `route_reason: "rule:<name>"`.

## Example rule

Pin every request that asked for the alias `demo-premium` to `openai/gpt-4o`.

:::code label="rule definition"
```json
{
  "name":        "premium_demo",
  "priority":    100,
  "enabled":     true,
  "action_kind": "pin",
  "match": { "requested_model": "demo-premium" },
  "action": {
    "provider": "openai",
    "model":    "gpt-4o"
  }
}
```
:::

## Rule fields

:::params
- name: name
  type: string
  required: true
  desc: Human identifier surfaced in trace metadata as `rule:<name>`.
- name: priority
  type: integer
  desc: Higher numbers evaluate first. Ties broken by insertion order.
- name: action_kind
  type: "pin | auto"
  desc: "`pin` sends to a fixed pair. `auto` defers to routeur.ai's automatic picker."
- name: match.requested_model
  type: string
  desc: Exact match against the caller's `model` field.
- name: action.provider
  type: string
  desc: Upstream provider identifier (`openai`, `gemini`, `deepseek`).
- name: action.model
  type: string
  desc: Concrete upstream model id.
:::

## Per-request overrides

Two headers let a single request bypass rule evaluation:

- `Routeur-Provider` — forces the provider.
- `Routeur-Model` — forces the model id.

Overrides still run through DLP and output moderation.
