# Changelog

All notable changes to the Tell & Go MCP server and this documentation
repository are documented here. The server is hosted at
`https://mcp.tellandgo.com/mcp`; this repo is documentation only and follows
[Keep a Changelog](https://keepachangelog.com/) conventions.

## [1.0.0] — 2026-07-24

### Added
- Initial public release of the Tell & Go MCP server.
- Four tools: `search_stays`, `get_stay_details`, `get_quote`, `start_booking`.
- Live markets: Maldives, Sri Lanka, Mauritius, Seychelles.
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
