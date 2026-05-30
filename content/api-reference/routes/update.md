---
title: Update a route
description: Partially update a routing rule.
operationId: updateRoute
section: Routes
prev:
  label: Replace route
  href: /api-reference/routes/replace
next:
  label: Delete route
  href: /api-reference/routes/delete
---

Partially update a routing rule. Only the fields you supply are changed; everything else is left as-is. Common use: flip `enabled` or adjust `priority`.

## Returns

The updated [Route object](/api-reference/routes/object).
