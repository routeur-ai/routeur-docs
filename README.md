# routeur.ai docs

[![docs.routeur.ai](https://img.shields.io/badge/docs-routeur.ai-7c3aed)](https://docs.routeur.ai)
[![License: CC BY-NC 4.0](https://img.shields.io/badge/license-CC%20BY--NC%204.0-3b82f6)](LICENSE)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-22c55e)](CONTRIBUTING.md)

The public source for **[docs.routeur.ai](https://docs.routeur.ai)** — the
documentation site for [routeur.ai](https://routeur.ai), the OpenAI-compatible
LLM gateway with routing, DLP, output moderation, traces, and per-request cost
attribution.

Open a PR to fix a typo, clarify a guide, add a code sample in your favourite
language, or sharpen an API description. Every page on the live site is
generated from a file in this repository.

## Layout

| Path                       | Contents                                                                |
| -------------------------- | ----------------------------------------------------------------------- |
| `content/guides/`          | Conceptual guides — quickstart, routing, DLP, traces, etc.              |
| `content/sdks/`            | One page per official SDK / language.                                   |
| `content/api-reference/`   | Per-endpoint reference, rendered alongside the OpenAPI spec.            |
| `openapi/`                 | OpenAPI 3.x spec (paths + components).                                  |
| `docs/DIRECTIVES.md`       | Reference for the custom markdown directives used throughout the site.  |
| `CONTRIBUTING.md`          | How to propose a change.                                                |
| `CODE_OF_CONDUCT.md`       | Community standards.                                                    |

## Quick start: edit a page

1. Fork this repo.
2. Edit any `.md` file under `content/` in your favourite markdown editor.
3. Push to your fork and open a pull request against `main`.
4. CI runs markdown lint, link check, and OpenAPI lint. Once green and
   approved, the change goes live the next time the docs site is built.

Most pages are plain CommonMark with a YAML front-matter header (title,
description, prev/next links). For richer layouts — tabbed code panels,
parameter tables, response examples, callouts — see
[docs/DIRECTIVES.md](docs/DIRECTIVES.md).

## Style

- Plain English. Short sentences. Examples over prose.
- The product name in prose is always **`routeur.ai`** — lowercase, with the
  `.ai` suffix. The code identifier `routeur` and the `Routeur-*` header
  family stay as-is in code blocks and request samples.
- Code samples should be real and runnable — no pseudocode.
- One sentence per line in source files so diffs are easy to review.

## Reporting an issue

Spot something inaccurate or missing? Open an issue using one of the templates
under [.github/ISSUE_TEMPLATE/](.github/ISSUE_TEMPLATE).

Security issues should be reported privately to **security@routeur.ai** —
please don't open a public issue for vulnerabilities in either the docs or the
product itself.

## License

Content is licensed under [CC BY-NC 4.0](LICENSE). You're free to copy and
adapt it for non-commercial use, as long as you give credit. Commercial reuse,
republication, or resale requires separate permission. The routeur.ai API and
product are governed separately by the
[routeur.ai Terms of Service](https://routeur.ai/legal/terms).
