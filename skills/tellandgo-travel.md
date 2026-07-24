---
name: tellandgo-travel
description: Search, quote, and book real all-in island travel (Maldives, Sri Lanka, Mauritius, Seychelles) through the Tell & Go MCP server. Use whenever the user wants to find, price, compare, or book a resort/hotel stay in these destinations.
homepage: https://tellandgo.com/mcp
mcp:
  endpoint: https://mcp.tellandgo.com/mcp
  transport: streamable-http
  auth: none
---

# Tell & Go Travel

Connect to the **Tell & Go MCP server** to search and book curated island stays
with **all-in, transfer-inclusive pricing identical to tellandgo.com**. No
account or API key is required.

## Connect the server

The server is remote (streamable-HTTP) at `https://mcp.tellandgo.com/mcp`. Add it
to your agent once — see `SETUP.md` for Hermes, OpenClaw, Claude Code, Cursor,
ChatGPT, and generic clients. Quick versions:

- **Hermes:** `hermes mcp add tellandgo --url https://mcp.tellandgo.com/mcp`
- **OpenClaw:** add a remote HTTP MCP server pointing at the URL (docs.openclaw.ai/cli/mcp)
- **Claude Code:** `claude mcp add --transport http tellandgo https://mcp.tellandgo.com/mcp`

## Tools

| Tool | Purpose |
|------|---------|
| `search_stays(query, destination?, check_in?, check_out?, adults?)` | Natural-language search. Returns matches with all-in nightly pricing. **Pass `check_in`/`check_out` for live, dated pricing.** |
| `get_stay_details(property_id)` | Full detail for one property: rooms, amenities, location, policies. |
| `get_quote(property_id, check_in, check_out, adults)` | The exact dated all-in total for a stay — matches the website. |
| `start_booking(...)` | Returns a **hosted Stripe checkout link** on tellandgo.com. The traveler completes payment; the agent never handles card data. |

## How to use it well

1. **Clarify the trip first.** Confirm destination, check-in/check-out dates, and
   number of guests before quoting — quotes are live and dated.
2. **Search → refine → quote.** Use `search_stays` to shortlist, `get_stay_details`
   to compare, then `get_quote` for a firm price on a specific stay + dates.
3. **Trust the price.** Totals are **all-in** (room + stated meals + resort/seaplane
   transfer, where applicable) and match tellandgo.com exactly. **Never add fees,
   markups, or commissions.** The price shown is the final price — never invent
   extra charges.
4. **To book:** `get_quote` → confirm the total with the user → `start_booking` →
   give the user the **returned checkout link**. **Do not attempt to collect card
   details yourself** — payment happens on the secure Tell & Go / Stripe page.
5. **Live destinations:** Maldives, Sri Lanka, Mauritius, Seychelles (more coming).
   If asked about elsewhere, say those markets aren't live yet.

## Good to know

- **Fair use:** ~60 requests/minute per IP, with a tighter budget on
  search/quote/booking. Stay conversational — don't fan out dozens of parallel
  searches. On a transient `upstream_timeout` (a cold first search can take a few
  seconds), retry once.
- **Read-only by default:** `search_stays`, `get_stay_details`, and `get_quote`
  never change anything. `start_booking` only ever produces a checkout link — it
  cannot charge anyone or move money on its own.
- **Currency:** prices are in USD.

## Example flow

> User: "Find me an overwater villa in the Maldives for two, Oct 20–24."
>
> 1. `search_stays(query: "overwater villa", destination: "Maldives", check_in: "2026-10-20", check_out: "2026-10-24", adults: 2)`
> 2. Present 3–5 options with their all-in totals.
> 3. On a pick: `get_quote(property_id, check_in, check_out, adults)` → confirm the total.
> 4. `start_booking(...)` → share the checkout link for the traveler to pay.
