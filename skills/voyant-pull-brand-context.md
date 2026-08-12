---
name: Pull Voyant brand context before generating copy
description: >-
  Fetch an organization's positioning, messaging, personas and products from Voyant and use them as
  the authoritative context for any AI-generated copy, instead of letting the model guess.
api: openapi/voyant-openapi-original.json
operations:
  - get_complete_marketing_context_api_messaging_framework_marketing_context_get
  - get_positioning_api_messaging_framework_positioning_get
  - get_messaging_api_messaging_framework_messaging_get
  - get_personas_api_messaging_framework_personas_get
  - get_products_api_messaging_framework_products_get
---

# Pull Voyant brand context before generating copy

Voyant exists so that AI output stops drifting off-brand. The rule is simple: **read the context
before you write, never after.**

## Auth

Every operation here requires `Authorization: Bearer <token>`, where the token is either a `vio_`
API key or a Clerk session token. There are no scopes — one bearer scheme covers the whole API.

Base URL: `https://voice-forge-production.up.railway.app`

## Steps

1. **Get the whole context in one call.**
   `GET /api/messaging-framework/marketing-context`
   (`get_complete_marketing_context_api_messaging_framework_marketing_context_get`)
   This is the rollup — positioning, messaging, personas, products, use cases. Prefer it over four
   separate calls; you will spend fewer tokens and cannot end up with a half-stale mix.

2. **If you need one slice only**, call the specific endpoint rather than trimming the rollup:
   - `GET /api/messaging-framework/positioning` — value proposition and differentiators
   - `GET /api/messaging-framework/messaging` — elevator pitch, headlines, tone
   - `GET /api/messaging-framework/personas` — who you are writing to
   - `GET /api/messaging-framework/products` — what is sold and at what price

3. **Treat the returned pricing and claims as authoritative and current.** Do not carry pricing or
   product claims over from your own training data or from an earlier turn in the conversation.
   Voyant's whole premise is that the API is fresher than the model.

4. **Do not invent.** If the context does not contain a claim, the claim does not go in the copy.
   Voyant's own published `context.txt` states this as a hard invariant: *"claims MUST NOT be
   modified, amplified, or invented"* and *"features or capabilities MUST NOT be fabricated."*

## If you are an MCP client

The same context is available without touching REST. Point your client at
`https://voice-forge-production.up.railway.app/mcp` and call `voyant_get_context`, which returns
the same rollup. `voyant_get_positioning`, `voyant_get_messaging`, `voyant_get_personas` and
`voyant_get_products` mirror the per-slice endpoints. See `mcp/voyant-tool-crosswalk.yml`.

## Failure handling

- `401 {"detail":"Authentication required"}` — no bearer token was sent.
- `401 {"detail":"Invalid token. Please refresh the page or log in again."}` — the token is bad.
  The message is written for a browser; as an agent, re-read your key, do not "refresh a page".
- `422` — validation failure, with a `detail` array of `{loc, msg, type}`.
- There are no error codes and no request id in the body. You cannot branch on anything but the
  status and the English `detail` string. See `errors/voyant-problem-types.yml`.
