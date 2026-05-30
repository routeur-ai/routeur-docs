---
title: List routes
description: Return every routing rule belonging to the authenticated organisation.
operationId: listRoutes
section: Routes
prev:
  label: The Route object
  href: /api-reference/routes/object
next:
  label: Create route
  href: /api-reference/routes/create
---

Return every routing rule belonging to the authenticated organisation, sorted by `priority` descending.

## Returns

An object with `data` (array of [Route objects](/api-reference/routes/object)) and `has_more` (boolean).
