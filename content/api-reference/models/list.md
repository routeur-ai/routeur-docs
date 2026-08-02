---
title: List models
description: Return every upstream model routeur.ai can currently route to.
operationId: listModels
section: Models
prev:
  label: The Model object
  href: /api-reference/models/object
next:
  label: Retrieve model
  href: /api-reference/models/retrieve
---

Return every upstream model routeur.ai can currently route to. OpenAI-compatible: callers built against `GET /v1/models` work without changes, which is what lets tools that probe this endpoint before they will talk to a base URL at all — the OpenAI SDKs, LangChain, LibreChat, Open WebUI, Continue and the editor integrations — point at routeur.ai unmodified.

## Returns

An object with `object: "list"` and `data` (array of [Model objects](/api-reference/models/object)).

Each entry carries OpenAI's fields (`id`, `object`, `created`, `owned_by`) so generic clients parse it, plus routeur.ai's own (`provider`, `context_window`, `pricing`, `configured`, `aliases`).

`created` is present for schema compatibility but is a fixed constant, not a release date — routeur.ai's catalogue tracks pricing and context windows, not model provenance. It is stable between calls, so a client that sorts or diffs on it behaves predictably; it just does not tell you when a model shipped.

`id` is unique within a response. Where two providers serve the same model id, the listing shows the one a pinned call to that id would actually route to.

## What you see

The listing is your workspace's, not a global vendor catalogue: it contains the models that workspace has activated, so two organisations calling this endpoint legitimately get different answers. `auto` is listed first — it is a real value you can send in the `model` field, and it asks the gateway to choose.

`configured` reports whether a credential for that model's provider is currently mounted. An activated model whose provider key has been removed is listed with `configured: false` rather than hidden, so a failed call has a visible explanation. The `?configured=` query parameter filters on it in both directions — `true` for routable models, `false` for exactly the ones that need a credential adding.

This listing covers chat models. Embedding models are resolved from a separate catalogue and are not activated per workspace, so they do not appear here — see [Create embeddings](/api-reference/embeddings/create) for the models that endpoint accepts.

This endpoint is not gated by billing or usage state — a capped or lapsed organisation can still list its models, because a client that cannot connect at all cannot discover why it is being refused.
