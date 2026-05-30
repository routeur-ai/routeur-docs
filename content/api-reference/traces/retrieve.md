---
title: Retrieve a trace
description: Fetch a single trace record by request_id.
operationId: retrieveTrace
section: Traces
prev:
  label: List traces
  href: /api-reference/traces/list
next:
  label: Inspect trace
  href: /api-reference/traces/inspect
---

Fetch a single trace record by `request_id`. When the trace has a `payload_url`, follow it to download the full payload archive — the URL is short-lived and signed; refetch the trace to mint a fresh one.

## Returns

The matching [Trace object](/api-reference/traces/object), or `404 not_found`.
