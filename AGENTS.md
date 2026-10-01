# Tell & Go MCP repository

This repository documents the public Tell & Go MCP server at `https://mcp.tellandgo.com/mcp`. It does not contain the running server. Agents working here should keep connection examples and tool instructions aligned with the live server card at `https://tellandgo.com/.well-known/mcp/server-card.json`.

For a traveler's request, use [the booking skill](skills/book-travel-with-tellandgo/SKILL.md). Search results are candidates, and only a live dated quote establishes a bookable rate. Explain the selected rate's meal plan, transfer inclusion, cancellation terms, currency, and total. Some rates include transfers and others do not. A changed prebook price needs the traveler's acceptance before proceeding. Payment belongs on the hosted checkout page; never collect or transmit card numbers in an agent conversation.

For documentation edits, do not promise fixed prices, guaranteed inventory, or transfer inclusion across every rate. Verify current behavior against the live MCP tools and Tell & Go's [agent guide](https://tellandgo.com/index.md), [pricing](https://tellandgo.com/pricing.md), and [authentication guide](https://tellandgo.com/auth.md).
