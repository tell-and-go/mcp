# Changelog

All notable changes to the Tell & Go MCP server and this documentation
repository are documented here. The server is hosted at
`https://mcp.tellandgo.com/mcp`; this repo is documentation only and follows
[Keep a Changelog](https://keepachangelog.com/) conventions.

## [Unreleased]

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
