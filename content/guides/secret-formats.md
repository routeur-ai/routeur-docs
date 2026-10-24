---
title: Secret formats
description: The 219 credential formats the DLP secrets detector can scan for, grouped by provider category.
section: Core concepts
prev:
  href: /guides/presets
  label: Presets reference
next:
  href: /api-reference/chat-completions/create
  label: Chat completions
---

<!--
  GENERATED FILE - DO NOT EDIT.
  Source: app/resources/data/secret-formats.json
  Regenerate with: scripts/generate-secret-formats-doc.py
-->

The **Secrets** detector in a [DLP](/guides/dlp) policy finds credentials such as API keys, access tokens and private keys in prompts. You choose which formats a policy scans for. This page lists all 219 selectable formats.

## How the detector works

- **Based on Gitleaks.** The format library starts from the open-source [Gitleaks](https://github.com/gitleaks/gitleaks) ruleset (MIT licence) and is adapted by routeur.ai. We review each Gitleaks release and roll out the changes.
- **Single pass.** A keyword prefilter rules out most text cheaply, so only prompts that contain a relevant keyword are checked against a format's full pattern.
- **Additions.** routeur.ai adds private-key, GCP service-account and high-entropy generic detectors, and switches off rules that proved too noisy.
- **Select only what you use.** Choose the providers your organisation actually uses. Fewer formats means faster scanning.

When a secret matches, the action you chose applies (see [DLP actions](/guides/dlp#actions)). With **Redact**, the match becomes `[REDACTED:SECRET]` if you selected several formats.

## Formats

### Cloud & infrastructure

| Name | ID |
|---|---|
| Alibaba access key ID | `alibaba-access-key-id` |
| Alibaba secret key | `alibaba-secret-key` |
| AWS access token | `aws-access-token` |
| Cisco Meraki API key | `cisco-meraki-api-key` |
| Cloudflare API key | `cloudflare-api-key` |
| Cloudflare global API key | `cloudflare-global-api-key` |
| Cloudflare origin CA key | `cloudflare-origin-ca-key` |
| Defined Networking API token | `defined-networking-api-token` |
| DigitalOcean access token | `digitalocean-access-token` |
| DigitalOcean personal access token | `digitalocean-pat` |
| DigitalOcean refresh token | `digitalocean-refresh-token` |
| Fastly API token | `fastly-api-token` |
| Fly.io access token | `flyio-access-token` |
| GCP API key | `gcp-api-key` |
| HashiCorp Terraform API token | `hashicorp-tf-api-token` |
| Heroku API key | `heroku-api-key` |
| Heroku API key v2 | `heroku-api-key-v2` |
| Infracost API token | `infracost-api-token` |
| Netlify access token | `netlify-access-token` |
| OpenShift user token | `openshift-user-token` |
| Pulumi API token | `pulumi-api-token` |
| Scalingo API token | `scalingo-api-token` |
| Yandex access token | `yandex-access-token` |
| Yandex API key | `yandex-api-key` |
| Yandex AWS access token | `yandex-aws-access-token` |

### Source control & CI/CD

| Name | ID |
|---|---|
| Artifactory API key | `artifactory-api-key` |
| Artifactory reference token | `artifactory-reference-token` |
| Asana client ID | `asana-client-id` |
| Asana client secret | `asana-client-secret` |
| Atlassian API token | `atlassian-api-token` |
| Bitbucket client ID | `bitbucket-client-id` |
| Bitbucket client secret | `bitbucket-client-secret` |
| Clojars API token | `clojars-api-token` |
| Codecov access token | `codecov-access-token` |
| Drone CI access token | `droneci-access-token` |
| GitHub app token | `github-app-token` |
| GitHub Fine Grained personal access token | `github-fine-grained-pat` |
| GitHub OAuth | `github-oauth` |
| GitHub personal access token | `github-pat` |
| GitHub refresh token | `github-refresh-token` |
| GitLab CI/CD job token | `gitlab-cicd-job-token` |
| GitLab deploy token | `gitlab-deploy-token` |
| GitLab feature flag client token | `gitlab-feature-flag-client-token` |
| GitLab feed token | `gitlab-feed-token` |
| GitLab incoming mail token | `gitlab-incoming-mail-token` |
| GitLab Kubernetes agent token | `gitlab-kubernetes-agent-token` |
| GitLab OAuth app secret | `gitlab-oauth-app-secret` |
| GitLab personal access token | `gitlab-pat` |
| GitLab personal access token (routable) | `gitlab-pat-routable` |
| GitLab pipeline trigger token | `gitlab-ptt` |
| GitLab runner Authentication token | `gitlab-runner-authentication-token` |
| GitLab runner Authentication token (routable) | `gitlab-runner-authentication-token-routable` |
| GitLab runner registration token | `gitlab-rrt` |
| GitLab SCIM token | `gitlab-scim-token` |
| GitLab session cookie | `gitlab-session-cookie` |
| Gitter access token | `gitter-access-token` |
| Harness API key | `harness-api-key` |
| JFrog API key | `jfrog-api-key` |
| JFrog identity token | `jfrog-identity-token` |
| LaunchDarkly access token | `launchdarkly-access-token` |
| Linear API key | `linear-api-key` |
| Linear client secret | `linear-client-secret` |
| npm access token | `npm-access-token` |
| Octopus deploy API key | `octopus-deploy-api-key` |
| Postman API token | `postman-api-token` |
| Prefect API token | `prefect-api-token` |
| PyPI upload token | `pypi-upload-token` |
| ReadMe API token | `readme-api-token` |
| RubyGems API token | `rubygems-api-token` |
| SettleMint application access token | `settlemint-application-access-token` |
| SettleMint personal access token | `settlemint-personal-access-token` |
| SettleMint service access token | `settlemint-service-access-token` |
| Sidekiq secret | `sidekiq-secret` |
| Sidekiq sensitive URL | `sidekiq-sensitive-url` |
| Snyk API token | `snyk-api-token` |
| Sonar API token | `sonar-api-token` |
| Sourcegraph access token | `sourcegraph-access-token` |
| Travis CI access token | `travisci-access-token` |

### Communication & email

| Name | ID |
|---|---|
| Beamer API token | `beamer-api-token` |
| Discord API token | `discord-api-token` |
| Discord client ID | `discord-client-id` |
| Discord client secret | `discord-client-secret` |
| Facebook access token | `facebook-access-token` |
| Facebook Page access token | `facebook-page-access-token` |
| Facebook secret | `facebook-secret` |
| Flickr access token | `flickr-access-token` |
| Frame.io API token | `frameio-api-token` |
| HubSpot API key | `hubspot-api-key` |
| Intercom API key | `intercom-api-key` |
| LinkedIn client ID | `linkedin-client-id` |
| LinkedIn client secret | `linkedin-client-secret` |
| Mailchimp API key | `mailchimp-api-key` |
| Mailgun private API token | `mailgun-private-api-token` |
| Mailgun public key | `mailgun-pub-key` |
| Mailgun signing key | `mailgun-signing-key` |
| Mattermost access token | `mattermost-access-token` |
| MessageBird API token | `messagebird-api-token` |
| MessageBird client ID | `messagebird-client-id` |
| Microsoft Teams webhook | `microsoft-teams-webhook` |
| New York Times access token | `nytimes-access-token` |
| Sendbird access token | `sendbird-access-token` |
| SendGrid API token | `sendgrid-api-token` |
| Sendinblue API token | `sendinblue-api-token` |
| Slack app token | `slack-app-token` |
| Slack bot token | `slack-bot-token` |
| Slack config access token | `slack-config-access-token` |
| Slack config refresh token | `slack-config-refresh-token` |
| Slack legacy bot token | `slack-legacy-bot-token` |
| Slack legacy token | `slack-legacy-token` |
| Slack legacy workspace token | `slack-legacy-workspace-token` |
| Slack user token | `slack-user-token` |
| Slack webhook URL | `slack-webhook-url` |
| Telegram bot API token | `telegram-bot-api-token` |
| Twilio API key | `twilio-api-key` |
| Twitch API token | `twitch-api-token` |
| Twitter access secret | `twitter-access-secret` |
| Twitter access token | `twitter-access-token` |
| Twitter API key | `twitter-api-key` |
| Twitter API secret | `twitter-api-secret` |
| Twitter Bearer token | `twitter-bearer-token` |
| Typeform API token | `typeform-api-token` |
| Zendesk secret key | `zendesk-secret-key` |

### Payments & finance

| Name | ID |
|---|---|
| Bittrex access key | `bittrex-access-key` |
| Bittrex secret key | `bittrex-secret-key` |
| Coinbase access token | `coinbase-access-token` |
| Duffel API token | `duffel-api-token` |
| EasyPost API token | `easypost-api-token` |
| EasyPost test API token | `easypost-test-api-token` |
| Etsy access token | `etsy-access-token` |
| Finicity API token | `finicity-api-token` |
| Finicity client secret | `finicity-client-secret` |
| Finnhub access token | `finnhub-access-token` |
| Flutterwave encryption key | `flutterwave-encryption-key` |
| Flutterwave public key | `flutterwave-public-key` |
| Flutterwave secret key | `flutterwave-secret-key` |
| FreshBooks access token | `freshbooks-access-token` |
| GoCardless API token | `gocardless-api-token` |
| Kraken access token | `kraken-access-token` |
| KuCoin access token | `kucoin-access-token` |
| KuCoin secret key | `kucoin-secret-key` |
| Lob API key | `lob-api-key` |
| Lob public API key | `lob-pub-api-key` |
| Plaid API token | `plaid-api-token` |
| Plaid client ID | `plaid-client-id` |
| Plaid secret key | `plaid-secret-key` |
| Shippo API token | `shippo-api-token` |
| Shopify access token | `shopify-access-token` |
| Shopify custom access token | `shopify-custom-access-token` |
| Shopify private app access token | `shopify-private-app-access-token` |
| Shopify shared secret | `shopify-shared-secret` |
| Square access token | `square-access-token` |
| Squarespace access token | `squarespace-access-token` |
| Stripe access token | `stripe-access-token` |

### Databases & data

| Name | ID |
|---|---|
| Airtable API key | `airtable-api-key` |
| Airtable personal access token | `airtable-personnal-access-token` |
| Algolia API key | `algolia-api-key` |
| ClickHouse Cloud API secret key | `clickhouse-cloud-api-secret-key` |
| Confluent access token | `confluent-access-token` |
| Confluent secret key | `confluent-secret-key` |
| Contentful delivery API token | `contentful-delivery-api-token` |
| Databricks API token | `databricks-api-token` |
| Dropbox API token | `dropbox-api-token` |
| Dropbox long-lived API token | `dropbox-long-lived-api-token` |
| Dropbox short-lived API token | `dropbox-short-lived-api-token` |
| Looker client ID | `looker-client-id` |
| Looker client secret | `looker-client-secret` |
| Mapbox API token | `mapbox-api-token` |
| MaxMind licence key | `maxmind-license-key` |
| Notion API token | `notion-api-token` |
| PlanetScale API token | `planetscale-api-token` |
| PlanetScale OAuth token | `planetscale-oauth-token` |
| PlanetScale Password | `planetscale-password` |

### Auth & identity

| Name | ID |
|---|---|
| 1Password secret key | `1password-secret-key` |
| 1Password service account token | `1password-service-account-token` |
| 42 Intra client secret | `intra42-client-secret` |
| Adobe client ID | `adobe-client-id` |
| Adobe client secret | `adobe-client-secret` |
| Authress service client access key | `authress-service-client-access-key` |
| Azure AD client secret | `azure-ad-client-secret` |
| Curl auth header | `curl-auth-header` |
| Curl auth user | `curl-auth-user` |
| Doppler API token | `doppler-api-token` |
| Okta access token | `okta-access-token` |
| RapidAPI access token | `rapidapi-access-token` |
| Sendbird access ID | `sendbird-access-id` |
| Vault batch token | `vault-batch-token` |
| Vault service token | `vault-service-token` |

### AI & ML

| Name | ID |
|---|---|
| Anthropic admin API key | `anthropic-admin-api-key` |
| Anthropic API key | `anthropic-api-key` |
| AWS Amazon Bedrock API key long-lived | `aws-amazon-bedrock-api-key-long-lived` |
| AWS Amazon Bedrock API key short-lived | `aws-amazon-bedrock-api-key-short-lived` |
| Cohere API token | `cohere-api-token` |
| Hugging Face access token | `huggingface-access-token` |
| Hugging Face organisation API token | `huggingface-organization-api-token` |
| OpenAI API key | `openai-api-key` |
| Perplexity API key | `perplexity-api-key` |
| Private AI API token | `privateai-api-token` |

### Monitoring & analytics

| Name | ID |
|---|---|
| Datadog access token | `datadog-access-token` |
| Dynatrace API token | `dynatrace-api-token` |
| Grafana API key | `grafana-api-key` |
| Grafana Cloud API token | `grafana-cloud-api-token` |
| Grafana service account token | `grafana-service-account-token` |
| New Relic browser API token | `new-relic-browser-api-token` |
| New Relic insert key | `new-relic-insert-key` |
| New Relic user API ID | `new-relic-user-api-id` |
| New Relic user API key | `new-relic-user-api-key` |
| Sentry access token | `sentry-access-token` |
| Sentry organisation token | `sentry-org-token` |
| Sentry user token | `sentry-user-token` |
| Sumo Logic access ID | `sumologic-access-id` |
| Sumo Logic access token | `sumologic-access-token` |

### Private keys & tokens

| Name | ID |
|---|---|
| Age secret key | `age-secret-key` |
| GCP service-account key | `gcp_key` |
| JWT | `jwt` |
| JWT Base64 | `jwt-base64` |
| PEM private key | `private_key` |
| Private key | `private-key` |

### Other

| Name | ID |
|---|---|
| Adafruit API key | `adafruit-api-key` |
| Generic passwords & tokens (high-entropy values) | `generic_secret` |
