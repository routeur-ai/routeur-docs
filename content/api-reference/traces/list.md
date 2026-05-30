---
title: List traces
description: Return trace records for the authenticated organisation in reverse chronological order.
operationId: listTraces
section: Traces
prev:
  label: The Trace object
  href: /api-reference/traces/object
next:
  label: Retrieve trace
  href: /api-reference/traces/retrieve
---

Return trace records for the authenticated organisation in reverse chronological order. Use the cursor-style `starting_after` parameter to paginate.

## Returns

An object with `data` (array of [Trace objects](/api-reference/traces/object)) and `has_more`.
