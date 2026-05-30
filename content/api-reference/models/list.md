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

Return every upstream model routeur.ai can currently route to. OpenAI-compatible: callers built against `GET /v1/models` work without changes.

## Returns

An object with `data` (array of [Model objects](/api-reference/models/object)).
