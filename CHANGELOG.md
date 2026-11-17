# Changelog

All notable changes to the Tell & Go MCP server and this documentation
repository are documented here. The server is hosted at
`https://mcp.tellandgo.com/mcp`; this repo is documentation only and follows
[Keep a Changelog](https://keepachangelog.com/) conventions.

## [Unreleased]

### Added (2026-11-17)
- Tell & Go is listed in Claude's connector directory:
  <https://claude.ai/directory/connectors/tell-and-go>. The README and the setup guide link to it; adding the server by
  hand as a custom connector still works.

### Fixed (2026-11-16)
- Connecting a Tell & Go account from Claude on the web, desktop and mobile.
  The authorization server refused Claude's client metadata document because
  it lists a grant type the server does not offer. It now accepts the document
  and still issues tokens only for `authorization_code` and `refresh_token`.
  Claude Code was not affected.

### Changed (2026-11-16)
- `get_cancellation_instructions` answers as a normal result instead of
  `isError: true`. The result is `cancelled: false`,
  `cancellation_channel: "emailed_secure_link"` and a `message` for the
  traveler; `error_code` and `next_action` are gone from it. The unlisted
  `cancel_booking` name still answers `isError`, because it was asked to cancel
  and did not.
- The connected `cancel_booking` description now says what an unconnected call
  gets: a sign-in request when it names a `booking_id`.

### Changed (2026-11-12, Claude directory review)
- `save_quote` and `request_callback` use double opt-in: Tell & Go emails the
  traveler a confirmation link, and the team contacts them only after they
  click it. Until then the result has `status: "pending_confirmation"`, a
  `request_id` and `lead_id: null`. The consent object is still required.
- `cancel_booking` is renamed `get_cancellation_instructions`. It explains the
  secure-link cancellation and does not cancel bookings. The old name is still
  answered but no longer listed.
- Every tool carries a `title` annotation.
- Tool descriptions, server instructions and `search_stays` results describe
  the `refine` block as data (an optional question with a suggested
  `default_answer`); they no longer tell the assistant whether to ask it.
  `refine.if_delegated` and `refine.request_complete_note` are removed;
  `next_step` gains `missing`.

### Changed (2026-11-03)
- MCP Registry listing (1.0.1): the description no longer mentions hosted Stripe
  checkout; it says checkout is on tellandgo.com.
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
