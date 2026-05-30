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
