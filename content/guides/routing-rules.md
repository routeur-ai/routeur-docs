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

1. Filter to enabled rules that are in scope for the calling key's workspace and project.
2. Sort by `priority` **ascending — the lowest number is evaluated first.** The built-in default rule sits at `1000`, so a rule you want to win should use a number below it.
3. Evaluate `match` against the request. All present `match` keys are AND-ed; an empty `match` is a catch-all. First hit wins.
4. Apply the rule's `action`; record `route_reason: "rule:<slug>"`.

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
  desc: Human identifier for the rule. Trace metadata carries the rule's slug as `rule:<slug>`.
- name: priority
  type: integer
  desc: "0–9999. **Lower evaluates first.** The default rule sits at 1000. Ties broken by insertion order."
- name: action_kind
  type: "pin | auto | failover"
  desc: "`pin` sends to a fixed pair. `auto` defers to routeur.ai's automatic picker. `failover` tries an ordered or raced list of targets."
- name: match.requested_model
  type: string
  desc: Exact match against the caller's `model` field.
- name: match.min_input_tokens
  type: integer
  desc: Matches only when the estimated input token count is at or above this.
- name: match.contains_image
  type: boolean
  desc: Matches on whether the request carries image content.
- name: action.provider
  type: string
  desc: "Upstream provider identifier — the key in the provider catalogue, e.g. `openai`, `anthropic`, `google`."
- name: action.model
  type: string
  desc: Concrete upstream model id.
:::

## Per-request overrides

Two headers let a single request bypass rule evaluation:

- `Routeur-Provider` — forces the provider.
- `Routeur-Model` — forces the model id.

Overrides still run through DLP, prompt shields and output moderation.

## Failover rules

An `action_kind: "failover"` rule carries an ordered `targets` list plus a
`strategy` (`sequential` or `race`), an `on_error` policy and `max_parallel`.
`sequential` tries each target in turn until one succeeds; `race` dispatches in
parallel and takes the first healthy response.
