# HuiTravel MCP

[中文说明](./README.zh-CN.md) | [English](./README.en.md)

HuiTravel provides remote MCP tools for searching hotels and attraction tickets for authenticated B2B users.

Official website: [https://b2b-mcp.huitravel.com/](https://b2b-mcp.huitravel.com/)

MCP Registry listing: [https://registry.modelcontextprotocol.io/?q=huitravel-mcp](https://registry.modelcontextprotocol.io/?q=huitravel-mcp)

ModelScope MCP listing: [https://modelscope.cn/mcp/servers/HZJLPTB/huitravel-mcp](https://modelscope.cn/mcp/servers/HZJLPTB/huitravel-mcp)

The server currently exposes two read-only tools:

| Tool            | Purpose                                                                                                            |
| --------------- | ------------------------------------------------------------------------------------------------------------------ |
| `hotel_search`  | Search hotel candidates and reference rates by destination, hotel name, brand, star rating, budget, and stay dates |
| `ticket_search` | Search attraction and ticket candidates by city or attraction name                                                 |

The service does not create orders or support payment, cancellation, refunds, or order after-sales operations. Hotel reference rates do not represent real-time room prices or inventory. Ticket minimum prices do not confirm real-time availability or a successful booking.

## Connection

Configure a Streamable HTTP and OAuth-compatible MCP client with:

```json
{
  "mcpServers": {
    "huitravel-mcp": {
      "type": "streamableHttp",
      "url": "https://b2b-mcp.huitravel.com/mcp",
      "timeout": 30000
    }
  }
}
```

Every MCP request must be authorized with an OAuth Bearer access token. If a valid token is missing, the client can use the server's `resource_metadata` to discover the authorization requirements. Enterprise identity is determined by trusted authentication, never inferred from tool arguments or a shared business account.

## Tool Routing

### hotel_search

Use for city hotels, accommodation, hotel recommendations, filtering, or comparisons.

Required:

- `destination`: Chinese or English city name

Optional parameters include hotel name, brand, star rating, budget, check-in date, check-out date, and result limit.

Do not guess hotel names, brands, star ratings, budgets, commercial districts, or landmarks that the user did not provide. If no dates are provided, the service defaults to check-in on the current UTC date and check-out the following day; state the actual default dates in the response.

### ticket_search

Use for attractions, scenic spots, amusement parks, tickets, or ticket-price candidates.

Provide at least one of the following; you may provide both:

- `cityName`: city to search; if an attraction is specified, its city
- `scenicSpotKeyword`: attraction or amusement park name

Provide only `cityName` for a city-only query. Provide only `scenicSpotKeyword` for an attraction-only query. Never infer a city from model knowledge.

If no travel dates are provided, state the upstream default dates in the response; do not guess them.

## Result Handling

Successful responses may contain one or more of:

- Markdown text in MCP `content`
- Structured business data in `structuredContent`
- Optional MCP Apps card metadata

Prefer the Markdown text and cards returned by the tool, and preserve relevant structured data, limitations, images, and detail links. Do not create a card from structured data or invent missing fields. State empty results, authorization failures, and upstream outages explicitly; do not loosen constraints or retry repeatedly.

## Capability Boundaries

This service does not support:

- Booking, payment, cancellation, refund, or order after-sales
- Ticket type or traveler-type filtering
- Hotel occupancy, room count, commercial district, landmark, or sorting
- General travel guides, itinerary planning, transport, shows, or sports tickets
- Real-time inventory or confirmation that a specific date is bookable

Do not call the search tools for unsupported requests.

## Documentation

- [Chinese README](./README.zh-CN.md)
- [English tool design and routing](./docs/tool-design.en.md)
- [Chinese tool design and routing](./docs/tool-design.zh-CN.md)
- [English security policy](./SECURITY.md)
- [Chinese security policy](./SECURITY.zh-CN.md)
- [MIT License](./LICENSE)
