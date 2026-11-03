<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/logo-light.svg">
  <img alt="Tell & Go" src="assets/logo-light.svg" width="380">
</picture>

# Book real travel from any AI agent

**No account · No API key · Same prices as our site · Payment on Tell & Go's own checkout at tellandgo.com, which your agent never touches**

[![MCP](https://img.shields.io/badge/MCP-Streamable--HTTP-542fdb?style=flat-square)](https://modelcontextprotocol.io)
[![Auth](https://img.shields.io/badge/auth-none-22c55e?style=flat-square)](#connect)
[![Pricing](https://img.shields.io/badge/pricing-free-7e5eff?style=flat-square)](#pricing)
[![License: MIT](https://img.shields.io/badge/license-MIT-542fdb?style=flat-square)](./LICENSE)
[![Endpoint](https://img.shields.io/badge/endpoint-live-22c55e?style=flat-square)](https://mcp.tellandgo.com/mcp)

</div>

Tell & Go runs a public [Model Context Protocol](https://modelcontextprotocol.io)
server. Connect any AI agent — **Claude, ChatGPT, Cursor, Gemini CLI, Hermes,
OpenClaw, or your own** — and it can search live resort inventory, get exact
live dated quotes, and start a real booking that completes on our secure checkout page.

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

Prefer a ready-made pack? [`skills/`](./skills) has a standard
[`SKILL.md`](./skills/book-travel-with-tellandgo/SKILL.md), the legacy
[`tellandgo-travel.md`](./skills/tellandgo-travel.md), and per-client
[`SETUP.md`](./skills/SETUP.md). Coding agents can start with
[`AGENTS.md`](./AGENTS.md).

Install the standard skill into a supported coding agent with
`npx skills add https://github.com/tell-and-go/mcp --skill book-travel-with-tellandgo`.

---

## Why this exists

Booking.com and Expedia built AI agents **inside their own apps**. We did the
opposite: we opened our booking service as a **public MCP** that works in *every*
agent — before the big OTAs did.

| The question | The answer |
|---|---|
| **Is it real?** | Live inventory, real bookings, and confirmation email. A dated quote shows the selected rate's total and terms, including whether resort transfers are included. |
| **Is it safe?** | Search & quote are read-only. Booking only returns a checkout link on `tellandgo.com`, where the traveler enters guest details and pays. The agent **never** sees card data. |
| **What's the catch?** | None. Website rates, no markup for agent users, no account, MIT-licensed docs. |
| **Why not Booking's agent?** | Theirs is a closed app inside one assistant. Ours works in *every* client that speaks MCP. |

---

## Tools

| Tool | What it does |
|---|---|
| `search_stays` | Natural-language search over hotel and resort inventory; returns candidate stays and indicative pricing when available. Obtain a live quote to confirm bookability and transfer terms. |
| `get_stay_details` | Full detail for one property: rooms, amenities, location, policies, indicative pricing. |
| `get_quote` | A live, dated quote for a specific stay, dates, and guests, with the selected rate's total and terms. |
| `get_price_facts` | Read-only published price facts: the dated sample totals a resort's answer page shows. Not a quote. |
| `start_booking` | From a `quote_id` and the traveler's `residency`, returns a checkout link on tellandgo.com. The traveler enters guest details, sees the final total and pays there. The agent never handles card data. |
| `prebook_stay` | Optional. Re-checks the quoted rate and terms with the supplier before checkout. It does not hold the rate. |
| `get_booking` / `cancel_booking` | Status of a booking made in this conversation, and cancellation under the rate's terms. |
| `save_quote` / `request_callback` | Hand the traveler to a person on the team, only after they explicitly agreed to be contacted. Nothing is booked. |

**Typical flow:** `search_stays` → `get_stay_details` → `get_quote` → `start_booking`. Search results are paged: when `pagination.has_more` is true, call again with `next_offset` before saying you have shown every option. `budget_min`/`budget_max` are per night, so divide a whole-stay budget by the number of nights.

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

Follow up with `get_quote` for an exact dated total, then `start_booking`
with the `quote_id` and `residency` to receive the tellandgo.com checkout link
the traveler completes.

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

## What it can book

Curated, verified-bookable hotel & resort stays. The live set of destinations
grows all the time — see the current coverage at
**[tellandgo.com/en/destinations](https://tellandgo.com/en/destinations)**.

> Each new market simply grows the catalog behind the same endpoint — your agent
> doesn't need to change anything.

---

## Pricing

**Free to connect.** No account, no API key, no cost. Prices shown by the tools
follow the same live rate and checkout flow as tellandgo.com. Check each
quoted rate's meal plan, transfer inclusion, taxes, cancellation terms, and
total before presenting it to the traveler. We never add a special markup for
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
The live quoted rate is the source of truth. Transfer inclusion and meal plans
depend on the selected rate.
</details>

<details>
<summary><b>How does payment work?</b></summary>
<code>start_booking</code> returns a checkout link on tellandgo.com. The
traveler enters guest details, sees the final total and pays there. The agent
never touches card data. No booking exists until the traveler completes
checkout; on a Reserve now, pay later rate that happens with nothing charged
today.
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
- 🗂️ **Find it on:** [Official MCP Registry](https://registry.modelcontextprotocol.io/v0/servers?search=io.github.tell-and-go/mcp) (`io.github.tell-and-go/mcp`) · [Smithery](https://smithery.ai/servers/tellandgo/hotels) · [Glama](https://glama.ai/mcp/servers/tell-and-go/mcp)
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
