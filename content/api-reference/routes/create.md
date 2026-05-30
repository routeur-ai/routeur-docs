---
title: Create a route
description: Add a new routing rule.
operationId: createRoute
section: Routes
prev:
  label: List routes
  href: /api-reference/routes/list
next:
  label: Retrieve route
  href: /api-reference/routes/retrieve
---

Add a new routing rule. The rule is active for new requests within a few seconds — the gateway re-reads rules at a short polling interval.

## Returns

The newly created [Route object](/api-reference/routes/object).
