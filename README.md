# Tell & Go MCP

**Book real travel from any AI agent.**

Tell & Go runs a public [Model Context Protocol](https://modelcontextprotocol.io)
(MCP) server. Connect any AI agent — Claude, ChatGPT, Cursor, Gemini, or your own
— and it can **search, quote, and book** real island stays from Tell & Go
inventory.

- **No account, no API key, free to connect.**
- **Same prices as our website** — all-in and transfer-inclusive (room + stated
  meal plan + resort/seaplane transfer in one upfront number).
- **Safe by design** — search and quote are read-only; booking returns a hosted
  Stripe checkout link on our own domain. The agent never handles card data.

> This repository is **documentation only** — the MCP server itself is hosted at
> `https://mcp.tellandgo.com/mcp`. There is nothing to install or run.

---

## Connect

| Field | Value |
|-------|-------|
| Endpoint | `https://mcp.tellandgo.com/mcp` |
| Transport | `streamable-http` (HTTP) |
| Authentication | none |
| Landing page | https://tellandgo.com/mcp?src=github |

### Claude Code

```bash
claude mcp add --transport http tellandgo https://mcp.tellandgo.com/mcp
```

### Claude.ai / Claude Desktop

Settings → Connectors → **Add custom connector** → URL:

```
https://mcp.tellandgo.com/mcp
```

Leave the authentication fields empty.

### ChatGPT

Settings → Connectors (enable developer mode if prompted) → **Add** → custom MCP
server → URL `https://mcp.tellandgo.com/mcp`.

> Availability of custom MCP connectors depends on your ChatGPT plan.

### Cursor / VS Code / Windsurf / Gemini CLI

Add an `mcpServers` entry to your client's MCP config
(`~/.cursor/mcp.json`, VS Code MCP settings, etc.):

```json
{
  "mcpServers": {
    "tellandgo": {
      "url": "https://mcp.tellandgo.com/mcp"
    }
  }
}
```

### Hermes (Nous Research)

```bash
hermes mcp add tellandgo --url https://mcp.tellandgo.com/mcp
```

### OpenClaw

Add a remote HTTP MCP server pointing at `https://mcp.tellandgo.com/mcp` in your
OpenClaw MCP config (see `docs.openclaw.ai/cli/mcp`).

### Any MCP client

Point any spec-compliant MCP client at `https://mcp.tellandgo.com/mcp` over the
streamable-HTTP transport. No credentials required.

---

## Drop-in skill

Prefer a ready-made pack? [`skills/`](./skills) has:

- [`tellandgo-travel.md`](./skills/tellandgo-travel.md) — a portable agent skill
  that teaches your assistant how to search, quote, and book correctly (drop it
  into Hermes, OpenClaw, Claude Code `~/.claude/skills/`, etc.).
- [`SETUP.md`](./skills/SETUP.md) — copy-paste connect steps for every client.

---

## Fair use

~60 requests/minute per IP, a tighter budget on search/quote/booking calls, and a
global ceiling across all traffic. Excess traffic gets `429` + `Retry-After`.
Building a product on this? Reach out for higher throughput.

---

## Tools

| Tool | Description |
|------|-------------|
| `search_stays` | Natural-language search over bookable island inventory; returns matches with all-in, transfer-inclusive nightly pricing. |
| `get_stay_details` | Full detail for one property: rooms, amenities, location, policies, and indicative pricing. |
| `get_quote` | A live, dated quote for a specific stay + dates + guests — the exact all-in total, matching the website. |
| `start_booking` | Returns a hosted Stripe checkout link on tellandgo.com. The agent never handles card data; the traveler completes payment securely. |

Typical flow: `search_stays` → `get_stay_details` → `get_quote` → `start_booking`.

---

## Example JSON-RPC calls

The MCP transport speaks JSON-RPC 2.0 over streamable-HTTP.

**Initialize the session**

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2025-06-18",
    "capabilities": {},
    "clientInfo": { "name": "my-agent", "version": "1.0.0" }
  }
}
```

**List available tools**

```json
{ "jsonrpc": "2.0", "id": 2, "method": "tools/list", "params": {} }
```

**Search for stays**

```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "method": "tools/call",
  "params": {
    "name": "search_stays",
    "arguments": {
      "query": "overwater villa in the Maldives for a honeymoon in October, all-in with seaplane transfer",
      "check_in": "2026-10-12",
      "check_out": "2026-10-19",
      "adults": 2
    }
  }
}
```

Follow up with `get_quote` for an exact dated total, then `start_booking` to
receive the hosted checkout link the traveler completes.

---

## What it can book today

Curated, verified-bookable island stays across:

- Maldives
- Sri Lanka
- Mauritius
- Seychelles

More markets are coming soon — each new market simply grows the catalog behind the
same endpoint.

---

## FAQ

**Is it free?** Yes. No account, no API key, no cost to connect.

**Are the prices the same as the website?** Identical — markup-inclusive and
all-in (room + meal plan + transfer).

**How does payment work?** `start_booking` returns a hosted Stripe checkout link
on tellandgo.com. The agent never touches card data.

**Is it safe?** Search and quote are read-only. Booking only produces a checkout
link you choose to complete — the MCP cannot charge anyone on its own.

---

## Links

- Landing page: https://tellandgo.com/mcp
- Discovery manifest: https://tellandgo.com/.well-known/mcp.json
- `llms.txt`: https://tellandgo.com/llms.txt
- `llms-full.txt`: https://tellandgo.com/llms-full.txt

---

## Changelog

### 1.0.0
- Initial public release of the Tell & Go MCP server.
- Tools: `search_stays`, `get_stay_details`, `get_quote`, `start_booking`.
- Markets: Maldives, Sri Lanka, Mauritius, Seychelles.

---

## License

Documentation and example snippets in this repository are released under the MIT
License. See [`LICENSE`](./LICENSE).
