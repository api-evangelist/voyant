---
name: Track competitors and market signals with Voyant
description: >-
  Register competitors, harvest signals from GitHub, Reddit, HackerNews, Discord, YouTube, G2 and US
  government feeds, and read the results back as battlecards and competitive context.
api: openapi/voyant-openapi-original.json
operations:
  - list_competitors_api_competitors_get
  - add_competitor_api_competitors_post
  - trigger_scan_api_competitors__competitor_id__scan_post
  - get_scan_progress_api_competitors__competitor_id__scan_progress_get
  - get_battlecard_api_competitors__competitor_id__battlecard_get
  - get_competitor_context_api_competitors_context_stream_get
  - discover_signals_api_signals_discover_post
  - list_signals_api_signals_list_get
  - get_signal_trends_api_signals_trends_get
---

# Track competitors and market signals with Voyant

This is the intelligence half of Voyant: what the market and your competitors are doing, harvested
into the same context store the content side reads from.

Base URL: `https://voice-forge-production.up.railway.app`. Bearer token required throughout.

## Competitors

1. `GET /api/competitors` (`list_competitors_api_competitors_get`) — what is already tracked.
2. `POST /api/competitors` (`add_competitor_api_competitors_post`) to add one, or
   `POST /api/competitors/add-multiple` for a batch. `POST /api/competitors/discover`
   (`discover_competitors_simple_api_competitors_discover_post`) will propose them for you.
3. `POST /api/competitors/{competitor_id}/scan` (`trigger_scan_api_competitors__competitor_id__scan_post`)
   starts a scan. Scans are asynchronous — poll
   `GET /api/competitors/{competitor_id}/scan/progress`
   (`get_scan_progress_api_competitors__competitor_id__scan_progress_get`) rather than assuming the
   POST returned finished data. Use `POST /api/competitors/{competitor_id}/deep-scan` for the
   heavier pass.
4. Read results:
   - `GET /api/competitors/{competitor_id}/battlecard` — the sales-facing summary
   - `GET /api/competitors/{competitor_id}/strategic-moves`, `/history`, `/jobs`, `/linkedin`,
     `/authors`, `/youtube-channel` — the evidence behind it
   - `GET /api/competitors/context/stream` (`get_competitor_context_api_competitors_context_stream_get`)
     — competitive intelligence shaped as context for generation

## Signals

- `POST /api/signals/discover` (`discover_signals_api_signals_discover_post`) runs a harvest.
  Per-source variants exist: `/api/signals/discord/discover`, `/api/signals/gov/discover/sam`,
  `/api/signals/gov/discover/ferc`, `/api/signals/gov/discover/all`.
- `GET /api/signals/list` (`list_signals_api_signals_list_get`) reads them back; filter by `source`,
  `days` and `limit`.
- `GET /api/signals/trends` and `GET /api/signals/competitor-trends` give the movement rather than
  the raw items.
- Manage what gets harvested with `GET/POST /api/signals/sources` and
  `POST /api/signals/sources/load-preset/{preset_name}`.

## Two things to be careful about

**Pagination is thin.** `limit` is widely supported; `offset` is not. Most list endpoints cap the
window instead of paging it, and no endpoint returns a total count or a next-page token. Do not
assume you have seen the whole collection. See `conventions/voyant-conventions.yml`.

**Scans and harvests are consequential.** They dispatch outbound crawls and third-party API calls
under the organization's account. Treat every `discover`, `scan`, `deep-scan` and `scan/all`
operation as a write, not a read — `agentic-access/voyant-agentic-access.yml` classifies them the
same way. `POST /api/competitors/scan/all` fans out across every tracked competitor at once; do not
call it speculatively.

## For MCP clients

`voyant_get_signals` and `voyant_get_competitive_intel` cover the **read** side only. Nothing in the
15-tool MCP surface triggers a scan or a harvest, and nothing manages sources. That is a reasonable
boundary — the expensive, outbound-effect operations stay on REST behind a deliberate call.
