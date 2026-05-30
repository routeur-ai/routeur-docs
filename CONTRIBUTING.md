# Contributing to routeur.ai docs

Thanks for taking the time to improve the docs. Anything from a typo fix to a
new SDK quickstart is welcome. This guide covers how to propose a change.

## Ground rules

- Be kind. See [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).
- One logical change per pull request. Smaller PRs are reviewed faster.
- Don't open a public issue for security problems — email
  **security@routeur.ai** instead.

## What lives here

Only documentation:

- `content/` — every page on the live site (guides, SDK pages, API reference).
- `openapi/` — the OpenAPI 3.x spec for the routeur.ai HTTP API.

You do not need access to the docs-site build or deployment system to
contribute here. Editing markdown and the OpenAPI spec is enough.

## Workflow

1. **Fork** this repository to your own GitHub account.
2. **Create a branch** off `main` with a short, descriptive name
   (`fix-quickstart-typo`, `add-ruby-sdk-page`, `clarify-trace-fields`).
3. **Make the change**. Prefer one sentence per source line so diffs read
   cleanly in review.
4. **Run the checks** locally if you can (see [Local checks](#local-checks)).
5. **Push** your branch and **open a pull request** against `main` using the
   PR template.
6. CI will lint markdown, check links, and validate the OpenAPI spec. A
   maintainer will review, request any changes, and merge.

## Writing style

- **Plain English.** Active voice, present tense, short sentences.
- **One sentence per line** in the source file. Word wrap is the editor's
  problem, not the source file's.
- **Examples beat prose.** If you can show it in a code block, do.
- **Real, runnable code samples.** Use the actual base URL
  (`https://api.routeur.ai/v1`) and the actual env var (`ROUTEUR_KEY`).
- **Brand name.** In prose, always `routeur.ai` — lowercase, with the `.ai`
  suffix. The bare identifier `routeur` and the `Routeur-*` HTTP header
  family stay as-is inside code blocks.
- **No internal implementation details.** Describe what the product *does*
  for users, not how it does it under the hood. If you're unsure, ask in
  the PR.

## Custom directives

Some pages use custom `:::name` directives for tabbed code panels, parameter
tables, response examples, callouts, and grids. Their syntax is documented in
[docs/DIRECTIVES.md](docs/DIRECTIVES.md).

Plain markdown is always fine — the directives are optional enhancements.

## Local checks

These are the same checks CI runs. They're optional but help you iterate
without round-tripping a PR.

```bash
# Markdown lint
npx -y markdownlint-cli2 "content/**/*.md" "*.md"

# Broken-link check (online + offline references)
npx -y lychee --no-progress --exclude-loopback "content/**/*.md" "*.md"

# OpenAPI lint
npx -y @stoplight/spectral-cli lint openapi/openapi.yaml
```

You'll need Node 20+. None of the tools modify your files.

## Adding a new page

1. Create `content/<section>/<page>.md` with the standard front matter:

   ```yaml
   ---
   title: My new page
   description: One-line summary used as <meta description>.
   section: Get started
   prev:
     href: /guides/quickstart
     label: Quickstart
   next:
     href: /guides/authentication
     label: Authentication
   ---
   ```

2. Add a link to it from the nearest existing parent page.
3. If it's an API reference page, add the corresponding path / schema to
   `openapi/` and reference the `operationId` from the front matter.

## Adding a new language sample

Pop open the relevant page (e.g. `content/guides/quickstart.md` for the
quickstart tabbed snippet), find the `:::codetabs` block, and add a new
fence with the language tag. The renderer maps common tags to syntax
highlighting (curl, javascript, typescript, python, php, ruby, go, csharp,
java, yaml, json, bash, shell, xml, html).

## Releases

There isn't a formal release cycle. Merged PRs ship the next time the docs
site is rebuilt — typically within a day. There's no need to bump a version
number in this repository.

## Need help?

Open a draft PR and ask in the description, or start a
[discussion](https://github.com/routeur-ai/routeur-docs/discussions). For
account/product questions, see [routeur.ai/support](https://routeur.ai/support).
