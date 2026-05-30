# Markdown directives

Most pages in this repository are plain markdown with YAML front matter.

For richer layouts, the docs site also supports a small set of custom block directives.

Each directive starts with `:::name`, contains markdown, YAML, or code fences, and ends with a bare `:::` line.

## callout

Use `callout` for highlighted notes.

```md
:::callout violet
**Tip.** Use `auto` unless you need to pin a model.
:::
```

Supported styles:

- `violet`
- `amber`
- `type="violet"`
- `type="amber"`

Example:

```md
:::callout amber
**Preview.** Trace payload archives are available on selected plans.
:::
```

## codetabs

Use `codetabs` for tabbed, copyable code examples.

Put one fenced code block per language inside the directive.

```md
:::codetabs
```curl
curl https://api.routeur.ai/v1/models \
  -H "Authorization: Bearer $ROUTEUR_KEY"
```
```javascript
const res = await fetch("https://api.routeur.ai/v1/models", {
  headers: { Authorization: `Bearer ${process.env.ROUTEUR_KEY}` },
});
```
:::
```

Notes:

- The first fence becomes the default active tab.
- The fence language becomes both the tab label and the syntax-highlighting hint.
- Common aliases such as `curl`, `shell`, `bash`, `javascript`, `python`, `php`, `ruby`, `go`, `csharp`, and `java` are supported.

## code

Use `code` for a single framed code block with a header label.

```md
:::code label="request body"
```json
{
  "model": "auto",
  "messages": [{"role": "user", "content": "Say hello."}]
}
```
:::
```

If you omit `label`, the code-fence language is used.

## codegrid

Use `codegrid` to show multiple code blocks side by side.

This is useful for before/after comparisons, request/response pairs, or multi-stage flows.

```md
:::codegrid cols=2 label1="caller sends" label2="upstream sees"
```json
{"content": "Project code PROJ-ABC123"}
```
```json
{"content": "Project code [REDACTED]"}
```
:::
```

Options:

- `cols=2` or `cols=3` for the desktop grid width.
- `label1`, `label2`, `label3`, ... for per-panel headers.

If you omit a label, the fence language is used.

## response

Use `response` for API responses with an HTTP-status badge.

```md
:::response status=200 label="response"
```json
{
  "data": []
}
```
:::
```

Options:

- `status=200` sets the HTTP-status badge.
- `label="response"` sets the right-hand header text.
- `content-type="application/json"` overrides the content-type label.

If omitted:

- `status` defaults to `200`
- `label` defaults to `Response`
- `content-type` defaults to `application/json`

## params

Use `params` for parameter lists, field references, and error/status tables.

The body is YAML: one list item per row.

```md
:::params
- name: model
  type: string
  required: true
  desc: Model alias or provider model id.
- name: temperature
  type: number
  default: 1
  desc: Sampling temperature.
:::
```

Supported keys:

- `name`
- `type`
- `required: true`
- `default`
- `note`
- `desc`
- `status`
- `code`

Status/error rows can use `status` and `code` instead of `name`:

```md
:::params
- status: 404
  code: not_found
  desc: The requested resource does not exist.
:::
```

## cards

Use `cards` for small visual summaries arranged in a grid.

The body is YAML: one list item per card.

```md
:::cards cols=2
- label: Compatible
  value: OpenAI API
  desc: Existing SDKs work with a base URL swap.
- label: Traced
  desc: Every request records provider, model, latency, and cost.
:::
```

Supported keys:

- `label`
- `value`
- `desc`
- `cols=2`, `cols=3`, etc. on the directive header

## Front matter

Every page should start with YAML front matter.

Typical fields:

```yaml
---
title: Quickstart
description: Send your first chat completion through routeur.ai in under a minute.
section: Get started
prev:
  href: /guides/introduction
  label: Introduction
next:
  href: /guides/authentication
  label: Authentication
---
```

Common fields:

- `title`
- `description`
- `section`
- `prev.href`
- `prev.label`
- `next.href`
- `next.label`
- `operationId` for API-reference pages that correspond to an OpenAPI operation

## Guidelines

- Prefer plain markdown unless the directive clearly improves the page.
- Keep directive bodies simple and predictable.
- Use real API shapes and production URLs in examples.
- Avoid documenting private implementation details or internal infrastructure.
- If you need a new directive, open an issue first instead of inventing one in content.
