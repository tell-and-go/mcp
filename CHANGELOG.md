# Changelog

All notable changes to the Tell & Go MCP server and this documentation
repository are documented here. The server is hosted at
`https://mcp.tellandgo.com/mcp`; this repo is documentation only and follows
[Keep a Changelog](https://keepachangelog.com/) conventions.

## [Unreleased]

### Changed (2026-11-03)
- `start_booking` takes a `quote_id` and `residency` and returns a checkout link
  on tellandgo.com, where the traveler enters guest details and pays. Hosted
  Stripe Checkout and the `prebook_id` requirement are gone.
- `prebook_stay` is optional: it re-checks the rate and terms but does not hold
  the rate.
- Search results rank like tellandgo.com (best match first, in-budget stays
  ahead) and signal `next_offset` when more pages exist. Budgets are per night.
- Added `get_price_facts`, `get_booking`, `cancel_booking`, `save_quote` and
  `request_callback` to the tool docs, plus the standard agent skill
  (`skills/book-travel-with-tellandgo/SKILL.md`) and `AGENTS.md`.

### Added
- Fifth tool `prebook_stay` — locks the quoted rate with the supplier before
  payment and returns a `prebook_id` required by `start_booking`. Surfaces a
  `PRICE_CHANGED` status with a new `terms_digest` when the live price no
  longer matches the quote; re-call with `accepted_terms_digest` to accept it.
- Booking flow is now `search_stays` → `get_stay_details` → `get_quote` →
  `prebook_stay` → `start_booking`; `start_booking` requires the `prebook_id`
  returned by `prebook_stay`.

## [1.0.0] — 2026-07-24

### Added
- Initial public release of the Tell & Go MCP server.
- Four tools: `search_stays`, `get_stay_details`, `get_quote`, `start_booking`.
- Initial live markets: Maldives, Sri Lanka, Mauritius, Seychelles (see [tellandgo.com/en/destinations](https://tellandgo.com/en/destinations) for the current set).
- All-in, transfer-inclusive pricing (room + stated meal plan + resort/seaplane
  transfer in one upfront number), identical to tellandgo.com.
- Hosted Stripe checkout for payment — the agent never handles card data.
- No account, no API key, free to connect.
- Drop-in agent skill (`skills/tellandgo-travel.md`) + per-client setup guide
  (`skills/SETUP.md`).
- Discovery surfaces: `server.json` (registry manifest), `glama.json`,
  `/.well-known/mcp.json` (served at tellandgo.com), `llms.txt` / `llms-full.txt`.

### Security
- Rate limiting: 60 req/min/IP via Durable Object limiter.
- Request body cap 64 KB; upstream response cap 256 KB.
- `AGENT_API_KEY` never exposed in any response, error, or log.
- ANSI-escape defense and input trimming on all tool arguments.

[1.0.0]: https://github.com/tell-and-go/mcp/releases/tag/v1.0.0
