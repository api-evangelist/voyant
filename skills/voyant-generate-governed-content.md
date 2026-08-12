---
name: Generate content through Voyant and validate it before publishing
description: >-
  Produce persona-modulated copy using Voyant's RAG + messaging context, then run it through the
  governance validator so an off-brand or unsafe draft never leaves the agent.
api: openapi/voyant-openapi-original.json
operations:
  - hybrid_search_api_rag_search_post
  - generate_with_gypsum_and_rag_non_stream_api_rag_generate_with_gypsum_post
  - generate_with_hybrid_rag_api_rag_generate_post
  - get_modulator_context_api_context_modulators__modulator_id__context_get
  - validate_content_api_governance_validate_post
  - get_active_policy_api_governance_policy_active_get
---

# Generate content through Voyant and validate it before publishing

Generation and governance are two separate calls in this API. An agent that only makes the first
one is doing the thing Voyant was built to prevent.

Base URL: `https://voice-forge-production.up.railway.app`. Bearer token required on every step.

## Steps

1. **Shape the context for who you are writing to.**
   `GET /api/context-modulators/{modulator_id}/context`
   (`get_modulator_context_api_context_modulators__modulator_id__context_get`)
   List available modulators first with `GET /api/context-modulators`. A modulator binds a persona
   and a funnel stage to a set of context streams.

2. **Retrieve supporting material.**
   `POST /api/rag/search` (`hybrid_search_api_rag_search_post`) — semantic search over the
   organization's ingested content. `GET /api/rag/strategies` lists the retrieval strategies you
   may pass. Use this when the draft needs proof points, not just positioning.

3. **Generate.** Two entry points, and the choice matters:
   - `POST /api/rag/generate-with-gypsum`
     (`generate_with_gypsum_and_rag_non_stream_api_rag_generate_with_gypsum_post`) — RAG **plus**
     the messaging framework. This is the on-brand path. Use it by default.
   - `POST /api/rag/generate` (`generate_with_hybrid_rag_api_rag_generate_post`) — RAG only, no
     messaging framework. Faster, and off-brand by construction. Only use it when brand voice is
     genuinely irrelevant.

   Streaming variants exist (`/api/rag/generate-stream`,
   `/api/rag/generate-with-gypsum-stream`) if you are rendering to a user in real time.

4. **Validate before you publish. Do not skip this.**
   `POST /api/governance/validate` (`validate_content_api_governance_validate_post`) returns
   `is_safe`, a `risk_score` from 0.0 to 1.0, and a list of `violations`. Read the active policy
   first with `GET /api/governance/policy/active`
   (`get_active_policy_api_governance_policy_active_get`) so you know what it is checking.

   Gate on `is_safe`. If it is false, revise against the returned `violations` and re-validate —
   do not publish and do not explain the violation away.

## Note for MCP clients

`voyant_generate_content` covers step 3 and `voyant_search` covers step 2, but **there is no MCP
tool for the governance validator**. An MCP-only agent can generate through Voyant but cannot check
its own output. If you are operating over MCP and the draft is going somewhere public, call
`POST /api/governance/validate` over REST before publishing. See `mcp/voyant-tool-crosswalk.yml`.

## Constraints Voyant itself publishes

From `/.well-known/context.txt`, binding on any agent working with this content:

- Domain meaning must remain intact; claims must not be amplified or invented.
- Forward-looking statements must not be reframed as promises.
- Pricing and product details must reflect the current authoritative source — which is the API,
  not your training data.

Allowed: tone scaling, depth modulation, persona-appropriate vocabulary, reorganization, funnel-
stage framing. Prohibited: semantic mutation, new claims, roadmap invention, marketing embellishment.
