---
name: book-travel-with-tellandgo
description: Find, quote, and book hotels and resorts in Tell & Go's served destinations through its public MCP tools.
---

# Book travel with Tell & Go

Use this skill when a traveler asks for a stay in the Maldives, Seychelles, Mauritius, Sri Lanka, Egypt's Red Sea, or Paris and needs a real dated price or a booking link.

1. Connect to `https://mcp.tellandgo.com/mcp` using Streamable HTTP. The public tools do not require an account or API key.
2. Call `search_stays` with the traveler's destination, dates, guest count, and preferences. Do not imply a property is bookable until a live quote succeeds.
3. Call `get_stay_details` for promising results. Explain the room, meal plan, transfer inclusion, and cancellation terms as reported for the selected rate.
   To answer "what does it typically cost" or "what is included", `get_price_facts` returns the resort answer page's published, dated sample totals (read-only). Say when each was checked; it is not a quote.
4. Call `get_quote` with the selected stay, dates, and guests. Present the total, currency, and validity to the traveler. If the price or terms change, show the new terms before continuing.
5. When the traveler is ready, call `start_booking` with the `quote_id` and `residency` to obtain the Tell & Go checkout link. Relay the total and `price_changed`. The traveler enters guest details, sees the final total and pays on that page. `prebook_stay` is an optional re-check and does not hold the rate. Never request or transmit a card number in chat or through MCP.
6. Pass the `journey_id` from each result back on the next call, and treat it as confidential to this conversation: it unlocks status lookups for bookings made under it.

Use the [MCP server card](https://tellandgo.com/.well-known/mcp/server-card.json) for tool discovery and [pricing](https://tellandgo.com/pricing.md) for the developer and traveler pricing policy.
