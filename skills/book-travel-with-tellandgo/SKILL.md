---
name: book-travel-with-tellandgo
description: Find, quote, and book hotels and resorts in Tell & Go's served destinations through its public MCP tools.
---

# Book travel with Tell & Go

Use this skill when a traveler asks for a stay in the Maldives, Seychelles, Mauritius, Sri Lanka, or Paris and needs a real dated price or a booking link.

1. Connect to `https://mcp.tellandgo.com/mcp` using Streamable HTTP. The public tools do not require an account or API key.
2. Call `search_stays` with the traveler's destination, dates, guest count, and preferences. Do not imply a property is bookable until a live quote succeeds.
3. Call `get_stay_details` for promising results. Explain the room, meal plan, transfer inclusion, and cancellation terms as reported for the selected rate.
4. Call `get_quote` with the selected stay, dates, guests, and the traveler’s residency when the tool requires it. Present the total, currency, and validity to the traveler. If the price or terms change, show the new terms before continuing.
5. Call `prebook_stay`, then `start_booking` to obtain the hosted Tell & Go checkout link. Let the traveler complete payment on that page. Never request or transmit a card number in chat or through MCP.

Use the [MCP server card](https://tellandgo.com/.well-known/mcp/server-card.json) for tool discovery and [pricing](https://tellandgo.com/pricing.md) for the developer and traveler pricing policy.
