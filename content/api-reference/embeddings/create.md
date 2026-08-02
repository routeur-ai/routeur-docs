---
title: Create embeddings
description: Generate embedding vectors for one or more inputs. OpenAI-compatible.
operationId: createEmbeddings
section: Embeddings
prev:
  label: Create a chat completion
  href: /api-reference/chat-completions/create
next:
  label: Routes
  href: /api-reference/routes/list
---

Generate embedding vectors for one or more inputs. The request body is OpenAI-compatible, so an application already calling `POST /v1/embeddings` moves across by changing its key and base URL — the same two-line change as chat completions.

## Why route embeddings through the gateway

Retrieval-augmented applications send their *own documents* to an embedding model, usually in bulk, and usually at index time rather than in response to a user. That traffic is frequently the most sensitive an organisation has, and it is the traffic most often left pointing straight at the provider because only chat was migrated.

Routing it here means DLP and prompt-shield rules run on the input before it leaves your control, the spend appears on the same dashboard as the rest of your AI usage, and the calls land in the same audit trail.

## Returns

An object with `data` — one embedding per input, in the order the inputs were given. With `Routeur-Trace: true`, the response also includes a `routeur` block describing the route and any redactions applied.

## Differences from chat completions

- **`model` is required and never routed.** `auto` is not accepted. Vectors from two different models are not comparable, so silently switching model would corrupt an index rather than merely change a reply.
- **Pre-tokenised input is rejected.** OpenAI accepts arrays of token ids; routeur.ai does not, because token arrays carry no text for DLP to inspect. Send text and let the provider tokenise it.
- **No output moderation.** A vector carries no readable content to moderate. Input-side rules still apply in full.
- **Provider coverage.** OpenAI and any OpenAI-compatible endpoint (DeepSeek, Mistral, Together, Groq, self-hosted) and Google Gemini. Anthropic has no embeddings API; a request for a model it serves returns `400 embeddings_unsupported` rather than a confusing upstream failure.
- **A short batch is an error, not a partial result.** If an upstream returns fewer vectors than you sent inputs, the call fails rather than handing back a list whose indices no longer line up with your documents.

## Which models you can use

Embedding models are not part of the per-workspace model catalogue you activate for chat — that catalogue drives the auto-router, and an embedding model is never a routing candidate. They are resolved from routeur.ai's own embeddings catalogue instead, so they work without being activated:

| Provider | Models |
|---|---|
| `openai` | `text-embedding-3-small`, `text-embedding-3-large`, `text-embedding-ada-002` |
| `google` | `gemini-embedding-001`, `text-embedding-004` |
| `mistral` | `mistral-embed` |

A model your workspace *has* activated still resolves normally, which is how a self-hosted OpenAI-compatible embedding model is used. `GET /v1/models` lists the chat catalogue only.

## `encoding_format`

Both `float` (the default) and `base64` are supported, and `base64` matters more than it looks: the official OpenAI Python SDK sets it on every call unless you override it, so it is the default path for most callers.

The gateway always requests `float` from the upstream and re-encodes to your requested format itself. That keeps one response shape across providers whose support for the parameter differs — Gemini has no such parameter at all — and means the vectors are identical either way. Any other value returns `400 invalid_request`.
