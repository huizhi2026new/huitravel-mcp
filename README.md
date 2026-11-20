# HuiTravel MCP

[中文说明](./README.zh-CN.md) | [English](./README.en.md)

HuiTravel is a remote MCP service for authenticated B2B users to search hotel candidates and attraction-ticket candidates.

Official website: [https://b2b-mcp.huitravel.com/](https://b2b-mcp.huitravel.com/)

MCP Registry listing: [https://registry.modelcontextprotocol.io/?q=huitravel-mcp](https://registry.modelcontextprotocol.io/?q=huitravel-mcp)

ModelScope MCP listing: [https://modelscope.cn/mcp/servers/HZJLPTB/huitravel-mcp](https://modelscope.cn/mcp/servers/HZJLPTB/huitravel-mcp)

## Available tools

| Tool            | Purpose                                                                                                           |
| --------------- | ----------------------------------------------------------------------------------------------------------------- |
| `hotel_search`  | Search hotels by destination and supported filters such as hotel name, brand, star rating, budget, and stay dates |
| `ticket_search` | Search attractions and ticket candidates by city or attraction name                                               |

Both tools are read-only. The service does not support booking, payment, cancellation, refund, order after-sales, real-time inventory confirmation, or ticket availability confirmation.

## Quick connection

Configure a Streamable HTTP MCP client with:

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

Every MCP request must be authorized with an OAuth Bearer access token. If a valid token is missing, the client can use the server's `resource_metadata` to discover the authorization requirements.

## Important boundaries

- Hotel reference rates do not represent real-time room prices or inventory.
- Ticket minimum prices do not confirm real-time availability or a successful booking.
- Travel guides, itineraries, transport, shows, sports tickets, and order after-sales are outside the supported scope.
- Use only detail links returned by the service; never invent or substitute URLs.

## Documentation

- [Tool design and routing (English)](./docs/tool-design.en.md)
- [工具设计与路由（中文）](./docs/tool-design.zh-CN.md)
- [Security policy (English)](./SECURITY.md)
- [安全政策（中文）](./SECURITY.zh-CN.md)
- [MIT License](./LICENSE)
