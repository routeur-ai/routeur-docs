---
title: Inspect a trace
description: HEAD request returning trace metadata as response headers.
operationId: inspectTrace
section: Traces
prev:
  label: Retrieve trace
  href: /api-reference/traces/retrieve
next:
  label: List API keys
  href: /api-reference/api-keys/list
---

A lightweight HEAD request that returns metadata about a trace as response headers, without transferring the trace body. Useful for checking whether a payload archive exists before you fetch it.

## Returns

An empty body. Status is `200` when the trace exists, `404` otherwise.
