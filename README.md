<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/logo-light.svg">
  <img alt="Tell & Go" src="assets/logo-light.svg" width="380">
</picture>

# Book real travel from any AI agent

**No account · No API key · Same prices as our site · Payment on a hosted Stripe checkout your agent never touches**

[![MCP](https://img.shields.io/badge/MCP-Streamable--HTTP-542fdb?style=flat-square)](https://modelcontextprotocol.io)
[![Auth](https://img.shields.io/badge/auth-none-22c55e?style=flat-square)](#connect)
[![Pricing](https://img.shields.io/badge/pricing-free-7e5eff?style=flat-square)](#pricing)
[![License: MIT](https://img.shields.io/badge/license-MIT-542fdb?style=flat-square)](./LICENSE)
[![Endpoint](https://img.shields.io/badge/endpoint-live-22c55e?style=flat-square)](https://mcp.tellandgo.com/mcp)

</div>

Tell & Go runs a public [Model Context Protocol](https://modelcontextprotocol.io)
server. Connect any AI agent — **Claude, ChatGPT, Cursor, Gemini CLI, Hermes,
OpenClaw, or your own** — and it can search live resort inventory, get exact
all-in quotes, and start a real booking that completes on our secure checkout page.

> [!IMPORTANT]
> **This repository is documentation only.** The server is hosted at
> `https://mcp.tellandgo.com/mcp` — there is nothing to install or run.

---

## Connect in 30 seconds

One endpoint works in every MCP client:

| | Client | One-liner |
|---|---|---|
| 🟣 | **Claude Code** | `claude mcp add --transport http tellandgo https://mcp.tellandgo.com/mcp` |
| 🌐 | **Claude.ai / Desktop** | Settings → Connectors → Add custom connector → `https://mcp.tellandgo.com/mcp` (leave auth empty) |
| 💬 | **ChatGPT** | Settings → Connectors → Add custom MCP → `https://mcp.tellandgo.com/mcp` |
| ⚡ | **Cursor / VS Code / Windsurf / Gemini CLI** | add an `mcpServers` entry (below) |
| 🧠 | **Hermes** | `hermes mcp add tellandgo --url https://mcp.tellandgo.com/mcp` |
| 🦅 | **OpenClaw** | add a remote HTTP MCP server → `https://mcp.tellandgo.com/mcp` |

**Cursor / VS Code / Gemini CLI** — add to your MCP config
(`~/.cursor/mcp.json`, VS Code MCP settings, etc.):

```json
{
  "mcpServers": {
    "tellandgo": { "url": "https://mcp.tellandgo.com/mcp" }
  }
}
```

Prefer a ready-made pack? [`skills/`](./skills) has a drop-in agent skill
([`tellandgo-travel.md`](./skills/tellandgo-travel.md)) + per-client
[`SETUP.md`](./skills/SETUP.md).

---

## Why this exists

Booking.com and Expedia built AI agents **inside their own apps**. We did the
opposite: we opened our travel agency as a **public MCP** that works in *every*
agent — before the big OTAs did.

| The question | The answer |
|---|---|
| **Is it real?** | Live inventory, real bookings, confirmation email. An all-in Maldives quote *with the seaplane transfer included* — a number no OTA API surfaces. |
| **Is it safe?** | Search & quote are read-only. Booking only returns a hosted Stripe checkout URL on `tellandgo.com`. The agent **never** sees card data. |
| **What's the catch?** | None. Website rates, no markup for agent users, no account, MIT-licensed docs. |
| **Why not Booking's agent?** | Theirs is a closed app inside one assistant. Ours works in *every* client that speaks MCP. |

---

## Tools

| Tool | What it does |
|---|---|
| `search_stays` | Natural-language search over bookable island inventory; returns matches with all-in, transfer-inclusive nightly pricing. |
| `get_stay_details` | Full detail for one property: rooms, amenities, location, policies, indicative pricing. |
| `get_quote` | A live, dated quote for a specific stay + dates + guests — the exact all-in total, matching the website. |
| `start_booking` | Returns a hosted Stripe checkout link on tellandgo.com. The agent never handles card data; the traveler completes payment securely. |

**Typical flow:** `search_stays` → `get_stay_details` → `get_quote` → `start_booking`

<details>
<summary><b>Example JSON-RPC calls</b></summary>

The MCP transport speaks JSON-RPC 2.0 over streamable-HTTP.

**Initialize the session**

```json
{
  "jsonrpc": "2.0", "id": 1, "method": "initialize",
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
  "jsonrpc": "2.0", "id": 3, "method": "tools/call",
  "params": {
    "name": "search_stays",
    "arguments": {
      "query": "overwater villa in the Maldives for a honeymoon in October, all-in with seaplane transfer",
      "check_in": "2026-10-12", "check_out": "2026-10-19", "adults": 2
    }
  }
}
```

Follow up with `get_quote` for an exact dated total, then `start_booking` to
receive the hosted checkout link the traveler completes.

</details>

<details>
<summary><b>Try it with curl</b></summary>

```bash
# MCP handshake
curl -X POST https://mcp.tellandgo.com/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize"}'

# List tools
curl -X POST https://mcp.tellandgo.com/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list"}'
```

</details>

---

## What it can book today

Curated, verified-bookable island stays across:

| 🇲🇻 Maldives | 🇱🇰 Sri Lanka | 🇲🇺 Mauritius | 🇸🇨 Seychelles |
|:---:|:---:|:---:|:---:|

More markets are coming soon — each new market simply grows the catalog behind
the same endpoint.

---

## Pricing

**Free to connect.** No account, no API key, no cost. Prices shown by the tools
are **identical to tellandgo.com** — all-in (room + stated meal plan +
resort/seaplane transfer where applicable). We never add fees or markups for
agent users.

> [!NOTE]
> **Fair use:** ~60 requests/minute per IP, a tighter budget on
> search/quote/booking, and a global ceiling. Excess traffic gets `429` +
> `Retry-After`. Building a product on this? Reach out for higher throughput.

---

## FAQ

<details>
<summary><b>Is it free?</b></summary>
Yes. No account, no API key, no cost to connect.
</details>

<details>
<summary><b>Are the prices the same as the website?</b></summary>
Identical — markup-inclusive and all-in (room + meal plan + transfer).
</details>

<details>
<summary><b>How does payment work?</b></summary>
<code>start_booking</code> returns a hosted Stripe checkout link on
tellandgo.com. The agent never touches card data; the traveler completes payment
securely.
</details>

<details>
<summary><b>Is it safe?</b></summary>
Search and quote are read-only. Booking only produces a checkout link you choose
to complete — the MCP cannot charge anyone on its own. Tool annotations
(<code>readOnlyHint</code>, <code>openWorldHint</code>) are set honestly.
</details>

<details>
<summary><b>Which MCP spec version?</b></summary>
<code>2025-06-18</code>. The server also answers <code>2024-11-05</code> for
older clients.
</details>

---

## Links

- 🌐 **Landing page:** <https://tellandgo.com/mcp>
- 📋 **Discovery manifest:** <https://tellandgo.com/.well-known/mcp.json>
- 🤖 **`llms.txt`:** <https://tellandgo.com/llms.txt>
- 📖 **Full inventory reference:** <https://tellandgo.com/llms-full.txt>
- 📦 **Registry manifest:** [`server.json`](./server.json) · [`glama.json`](./glama.json)
- 📝 **Changelog:** [`CHANGELOG.md`](./CHANGELOG.md)

---

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/icon.svg">
  <img alt="Tell & Go" src="assets/icon.svg" width="56">
</picture>

**[Tell & Go](https://tellandgo.com)** · Made for agents, built for humans.

Documentation © 2026 Island Inventions, Inc. — released under the
[MIT License](./LICENSE).

</div>
