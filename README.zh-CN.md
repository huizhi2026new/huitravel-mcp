# 汇智酒旅 MCP（HuiTravel MCP）

[中文说明](./README.zh-CN.md) | [English](./README.en.md)

汇智酒旅（HuiTravel）为已通过 OAuth 认证的企业用户提供酒店和景区门票查询服务。

官方网站：[https://b2b-mcp.huitravel.com/](https://b2b-mcp.huitravel.com/)

MCP Registry 上架入口：[https://registry.modelcontextprotocol.io/?q=huitravel-mcp](https://registry.modelcontextprotocol.io/?q=huitravel-mcp)

魔搭 MCP 上架入口：[https://modelscope.cn/mcp/servers/HZJLPTB/huitravel-mcp](https://modelscope.cn/mcp/servers/HZJLPTB/huitravel-mcp)

当前版本提供两个只读工具：

| 工具            | 用途                                                                 |
| --------------- | -------------------------------------------------------------------- |
| `hotel_search`  | 按目的地、酒店关键词、品牌、星级、预算和入住日期查询酒店候选及参考价 |
| `ticket_search` | 按城市或景区关键词查询景区门票候选及最低售价                         |

服务不提供下单、支付、取消、退款或其他订单操作。酒店参考价不代表房型实时价格或库存，门票最低售价不代表指定日期实时可订或预订成功。

## 接入

在支持 Streamable HTTP 和 OAuth 的 MCP 客户端中配置：

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

每个 MCP 请求都必须携带有效的 OAuth Bearer 访问令牌。若令牌缺失或无效，客户端可根据服务端的 `resource_metadata` 发现授权要求并完成授权。企业身份由可信认证服务确定，不由工具参数或共享业务账号推断。

## 工具选择

### hotel_search

适用于城市酒店、住宿、酒店推荐、筛选或比较。

必填参数：

- `destination`：城市中文名或英文名

可选参数包括酒店名称、品牌、星级、预算、入住日期、离店日期和返回数量。酒店查询每组条件调用一次，不猜测未提供的酒店名称、品牌、星级、预算或商圈。

未提供入住日期时，服务默认按当前 UTC 日期入住、次日离店；回答时应说明实际采用的默认日期。

### ticket_search

适用于景区、景点、乐园及门票候选查询。

至少提供以下一项，也可以同时提供两项：

- `cityName`：查询城市；指定景区时为景区所在城市
- `scenicSpotKeyword`：景区或乐园名称

未提供出行日期时，由上游按默认规则查询。回答时应说明上游实际采用的默认日期，不要自行推断或编造。用户只提供城市时，只传 `cityName`；只提供景区时，只传 `scenicSpotKeyword`；不要由模型推断城市。

## 结果说明

成功响应可能包含以下一种或多种内容：

- MCP 文本中的同源 Markdown 展示
- `structuredContent` 中的结构化业务数据
- 可选 MCP Apps 卡片数据

优先展示工具返回的文本和卡片，并保留相关结构化数据、限制说明、图片及详情链接。不要根据结构化数据自行重建卡片或补造缺失字段。酒店和门票详情链接由受控配置生成。空结果、授权失败或上游不可用时，应明确说明；不要放宽条件或自动反复重试。

## 能力边界

本服务不支持：

- 下单、支付、取消、退款或订单售后
- 门票票种、人群和退改规则筛选
- 酒店入住人数、商圈、地标或排序
- 通用旅游攻略、行程规划、交通票务、演出或赛事票务
- 实时库存、实时房型价格、指定日期可订或预订成功确认

需要以上能力时，不调用本服务的查询工具。

## 项目文档

项目能力与路由设计见 [docs/tool-design.zh-CN.md](./docs/tool-design.zh-CN.md)。

## 安全文档

安全漏洞报告流程见 [SECURITY.zh-CN.md](./SECURITY.zh-CN.md)。不要在 Issue、日志或公开文档中提交 Access Token、客户端 Secret、私钥或客户数据。

## 许可

本项目使用 [MIT License](./LICENSE)。
