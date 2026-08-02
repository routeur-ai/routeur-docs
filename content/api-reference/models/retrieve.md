---
title: Retrieve a model
description: Fetch a single model by id.
operationId: retrieveModel
section: Models
prev:
  label: List models
  href: /api-reference/models/list
next:
  label: The Trace object
  href: /api-reference/traces/object
---

Fetch a single model by id. OpenAI-compatible — identical contract to `GET /v1/models/{id}` at api.openai.com.

## Returns

The matching [Model object](/api-reference/models/object), or `404 model_not_found`.
