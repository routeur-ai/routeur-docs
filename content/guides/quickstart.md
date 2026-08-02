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

Send your first chat completion through routeur.ai in three steps. The request shape is OpenAI-compatible, so any client that talks to `api.openai.com` can talk to `api.routeur.ai` with a base URL change and a different bearer token.

## 1. Get an API key

Sign in to the routeur.ai dashboard, open **Settings → API keys**, and click **Create key**. Copy the value once — routeur.ai stores only the hash. Set it in your shell:

:::codetabs
```shell
export ROUTEUR_KEY="sk-r-your_routeur_api_key"
```
:::

## 2. Send a chat completion

Pick a tab. Each example uses `auto` as the model alias, which lets routing rules pick the upstream model for you.

:::codetabs
```curl
curl https://api.routeur.ai/v1/chat/completions \
  -H "Authorization: Bearer $ROUTEUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "auto",
    "messages": [{"role": "user", "content": "Say hello."}]
  }'
```
```javascript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.ROUTEUR_KEY,
  baseURL: "https://api.routeur.ai/v1",
});

const r = await client.chat.completions.create({
  model: "auto",
  messages: [{ role: "user", content: "Say hello." }],
});
```
```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["ROUTEUR_KEY"],
    base_url="https://api.routeur.ai/v1",
)

r = client.chat.completions.create(
    model="auto",
    messages=[{"role": "user", "content": "Say hello."}],
)
```
```php
<?php
require 'vendor/autoload.php';

$client = OpenAI::factory()
    ->withApiKey(getenv('ROUTEUR_KEY'))
    ->withBaseUri('https://api.routeur.ai/v1')
    ->make();

$response = $client->chat()->create([
    'model' => 'auto',
    'messages' => [
        ['role' => 'user', 'content' => 'Say hello.'],
    ],
]);
```
```ruby
require "openai"

client = OpenAI::Client.new(
  access_token: ENV.fetch("ROUTEUR_KEY"),
  uri_base: "https://api.routeur.ai/v1",
)

response = client.chat(
  parameters: {
    model: "auto",
    messages: [{ role: "user", content: "Say hello." }],
  },
)
```
```go
client := openai.NewClient(
    option.WithAPIKey(os.Getenv("ROUTEUR_KEY")),
    option.WithBaseURL("https://api.routeur.ai/v1"),
)

resp, err := client.Chat.Completions.New(ctx, openai.ChatCompletionNewParams{
    Model: openai.F("auto"),
    Messages: openai.F([]openai.ChatCompletionMessageParamUnion{
        openai.UserMessage("Say hello."),
    }),
})
```
```csharp
using OpenAI;
using OpenAI.Chat;

var client = new ChatClient(
    model: "auto",
    credential: new ApiKeyCredential(Environment.GetEnvironmentVariable("ROUTEUR_KEY")!),
    options: new OpenAIClientOptions { Endpoint = new Uri("https://api.routeur.ai/v1") });

ChatCompletion completion = client.CompleteChat("Say hello.");
```
```java
OpenAIClient client = OpenAIOkHttpClient.builder()
    .apiKey(System.getenv("ROUTEUR_KEY"))
    .baseUrl("https://api.routeur.ai/v1")
    .build();

ChatCompletionCreateParams params = ChatCompletionCreateParams.builder()
    .model("auto")
    .addUserMessage("Say hello.")
    .build();

ChatCompletion completion = client.chat().completions().create(params);
```
:::

## 3. Inspect the route

Add `Routeur-Trace: true` to any request to receive a `routeur` block in the response describing which upstream was picked, how many redactions ran, and how much it cost.

:::response status=200 label="Response excerpt"
```json
{
  "choices": [{ "message": { "content": "Hello!" } }],
  "routeur": {
    "provider": "openai",
    "model": "gpt-4o-mini",
    "route_reason": "default",
    "latency_ms": 2939,
    "cost_usd": 0.00000615
  }
}
```
:::

:::callout violet
**Try a dry run.** Add `Routeur-Dry-Run: true` to see the route routeur.ai would have picked without calling the upstream provider or spending tokens.
:::

## 4. Discover which models you can use

`GET /v1/models` lists the models your workspace has activated, with pricing and whether a provider credential is currently mounted for each. This is also the endpoint tools probe before they will accept a custom base URL, so pointing LangChain, LibreChat, Open WebUI, Continue or an editor integration at `https://api.routeur.ai/v1` works with no further configuration.

:::code
```bash
curl https://api.routeur.ai/v1/models \
  -H "Authorization: Bearer $ROUTEUR_KEY"
```
:::

## 5. Move your embeddings across too

If you run retrieval, your indexing job is probably still calling the provider directly — which means the documents you embed never pass your policies. `POST /v1/embeddings` takes the same OpenAI-compatible body, applies your DLP and shield rules to the input, and records the spend on the same trace as everything else.

:::code
```bash
curl https://api.routeur.ai/v1/embeddings \
  -H "Authorization: Bearer $ROUTEUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"text-embedding-3-small","input":"The quick brown fox"}'
```
:::

:::callout amber
**Pin the model.** Unlike chat completions, `auto` is not accepted here — vectors from different models are not comparable, so switching model silently would corrupt an index rather than just change a reply.
:::
