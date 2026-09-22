# Add the Tell & Go MCP to your agent

The server is remote — **nothing to install or run**. Point your client at:

```
Endpoint:  https://mcp.tellandgo.com/mcp
Transport: streamable-http (HTTP)
Auth:      none
```

Pair this with the `tellandgo-travel.md` skill so your agent uses the tools well.

**[Landing page & one-paste setup](https://tellandgo.com/mcp?src=github)** — if you prefer a visual guide with copy buttons.

---

## Hermes (Nous Research)

One command:

```bash
hermes mcp add tellandgo --url https://mcp.tellandgo.com/mcp
```

Or in `~/.hermes/config.yaml`:

```yaml
mcp_servers:
  tellandgo:
    type: http
    url: https://mcp.tellandgo.com/mcp
```

Drop `tellandgo-travel.md` into your Hermes skills/instructions so the agent knows
how to search, quote, and book. The travel tools appear in your next session.

---

## OpenClaw

Add a **remote HTTP MCP server** in your OpenClaw MCP config (see
`docs.openclaw.ai/cli/mcp` for the exact location in your version):

```json
{
  "mcpServers": {
    "tellandgo": {
      "type": "http",
      "url": "https://mcp.tellandgo.com/mcp"
    }
  }
}
```

Reload OpenClaw — the `tellandgo` tools appear. Include `tellandgo-travel.md` as
a skill/context file so the agent follows the booking flow correctly.

---

## Claude Code

```bash
claude mcp add --transport http tellandgo https://mcp.tellandgo.com/mcp
```

For the skill, save `tellandgo-travel.md` under `~/.claude/skills/` (or your
project's `.claude/skills/`).

---

## Claude.ai / Claude Desktop

Settings → **Connectors** → **Add custom connector** → paste
`https://mcp.tellandgo.com/mcp`. Leave auth empty.

---

## ChatGPT

Settings → **Connectors** (enable developer mode if prompted) → **Add** a custom
MCP server → paste `https://mcp.tellandgo.com/mcp`. (Custom connectors depend on
your ChatGPT plan.)

---

## Cursor / VS Code / Windsurf / Gemini CLI

The same `mcpServers` JSON works everywhere (e.g. `~/.cursor/mcp.json`, VS Code
MCP settings, Windsurf, Gemini CLI):

```json
{
  "mcpServers": {
    "tellandgo": {
      "url": "https://mcp.tellandgo.com/mcp"
    }
  }
}
```

---

## Any spec-compliant MCP client

Connect over the **streamable-HTTP** transport to
`https://mcp.tellandgo.com/mcp`. No auth. Then call `search_stays`,
`get_stay_details`, `get_quote`, `prebook_stay`, and `start_booking`.

---

## Fair use

~60 requests/minute per IP, with a tighter budget on search/quote/booking and a
global ceiling across all traffic. Excess traffic receives `429` with a
`Retry-After` header. Building a product on this? Reach out for higher throughput.
