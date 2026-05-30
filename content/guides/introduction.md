---
title: Introduction
description: What routeur.ai is, how it fits between your application and upstream LLM providers, and what it does on every request.
section: Get started
next:
  href: /guides/quickstart
  label: Quickstart
---

Routeur is a single OpenAI-compatible endpoint that sits between your application and the upstream LLM providers you use today. Point your existing SDK at `https://api.routeur.ai/v1`, send the same chat completion request you send to OpenAI, and routeur.ai picks the cheapest capable model that satisfies your routing rules, applies DLP and output moderation, and returns the standard response shape your code already understands.

## What routeur.ai does on every request

Four things, in this order, on the hot path:

1. **Authenticate** the caller against a routeur.ai API key.
2. **Resolve a route** — pick the upstream provider and model based on the request and your rules.
3. **Apply input DLP** — redact or block sensitive substrings before the upstream sees them.
4. **Forward and moderate** — call the upstream provider, run output moderation on the result, return the standard OpenAI-shape response.

## Why a gateway

Three problems that every team running LLM features hits, and the one place to solve them.

:::cards cols=3
- label: Cost control
  desc: Centrally pick the cheapest model that meets the request — without redeploying client code.
- label: Data safety
  desc: DLP rules redact secrets, PII, and prompt-injection vectors before the upstream model ever sees them.
- label: Observability
  desc: Every request is traced with provider, model, latency, tokens, cost, and an optional full-payload archive.
:::

## Design principles

- **OpenAI-compatible at the edge.** Existing SDKs, fewer migrations.
- **Stateless hot path.** The gateway holds no per-request state; configuration is loaded at startup from a secure configuration store.
- **Short, JSON error bodies.** Routeur never relays raw upstream error pages back to your application.
- **Auditable.** Trace records and optional payload archives let you reconstruct any request.
