# Tool Design and Routing

[中文工具设计](./tool-design.zh-CN.md) | [English](./tool-design.en.md)

## Server Scope

The HuiTravel B2B MCP Server queries controlled hotel and attraction-ticket candidates. It does not promise real-time inventory, bookable status, or successful reservations.

Each MCP request uses an independent server instance. Every request must carry an OAuth Bearer access token, which is exchanged for an upstream OpenAPI token according to RFC 8693. Preserve upstream limitations and never exaggerate a candidate as a live offer.

## Routing

| User intent                                                                        | Tool                                                             |
| ---------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| Hotels, accommodation, where to stay, hotel filtering or comparison                | `hotel_search`                                                   |
| Attractions, scenic spots, amusement parks, tickets or ticket prices               | `ticket_search`                                                  |
| Brand mention without a search object                                              | Do not call a tool; ask whether the user needs hotels or tickets |
| Flights, transport, shows, sports, travel guides, itineraries or order after-sales | Do not call either tool                                          |

For a city-only query, hotel search receives only `destination`; ticket search receives only `cityName`. For an attraction-only query, ticket search receives only `scenicSpotKeyword`. Do not infer a city from model knowledge.

## hotel_search

Purpose: search hotel candidates by destination and supported filters.

Required:

- `destination`: 1–200 characters; Chinese or English city name

Optional:

- `hotel_name`: hotel-name keyword
- `brand`: brand keyword
- `stars`: up to five values from 0–5, allowing decimals
- `budget`: non-negative `min` and `max` values with an optional currency
- `checkin_date`: `YYYY-MM-DD`, not earlier than the current UTC date
- `checkout_date`: later than check-in
- `limit`: integer from 1–6, default 3

Unsupported: occupancy, room count, commercial districts, landmarks, and sorting.

When dates are omitted, default to check-in on the current UTC date and check-out the following day, and state the actual default dates in the response. Reference rates describe the daily starting-rate range for the stay period and are not room-level real-time prices or inventory.

## ticket_search

Purpose: search attraction-ticket candidates by city or attraction keyword.

Provide at least one of the following; you may provide both:

- `cityName`
- `scenicSpotKeyword`

Optional:

- `travelDateStart`: `YYYY-MM-DD`
- `travelDateEnd`: `YYYY-MM-DD`
- `budgetMin` and `budgetMax`: non-negative numeric values
- `limit`: 1–6, default 3

Unsupported: ticket type and traveler-type filtering.

Use one call per condition set. Do not split a city query into separate attraction searches. Call again only if the user changes conditions or requests a repeat search.

If dates are omitted, state the upstream default date behavior in the response. Preserve the upstream amount and currency; do not convert units or imply real-time availability.

## Results and Display

Successful responses may contain Markdown in MCP `content`, business data in `structuredContent`, and optional MCP Apps card metadata. Prefer the tool-returned Markdown and cards, and preserve relevant structured data, limitations, images, and detail links. Do not reconstruct cards from structured data or invent missing fields.

- Empty hotel or attraction result: state that nothing was found; do not relax filters
- Attraction exists but has no ticket products: state that no ticket products are available
- Invalid parameters: explain missing or invalid conditions
- Authentication or authorization failure: explain that authorization is required; do not expose credentials
- Upstream unavailable or rate limited: state that the service is temporarily unavailable; do not retry automatically
