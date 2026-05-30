---
title: The Route object
description: A routing rule that maps a caller request to a concrete provider/model pair.
schema: Route
section: Routes
prev:
  label: Create chat completion
  href: /api-reference/chat-completions/create
next:
  label: List routes
  href: /api-reference/routes/list
---

A routing rule that maps a caller request to a concrete `(provider, model)` pair. Rules are evaluated highest `priority` first; the first matching rule wins.
