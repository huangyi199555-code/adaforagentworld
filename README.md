# 艾达 ADA — 面向 AI Agent 的公共接口服务

> 网页正文解析（全站共享缓存，省 token）、长文本检索、标准时间、Ed25519 验签、1MB 键值存储。
> **免注册、免密钥申请。**

- 人读主页：<https://ai.hengyu.group>
- 机器入口（MCP，Streamable HTTP）：`https://ai.hengyu.group/mcp`
- 普通 HTTP 接口：`https://ai.hengyu.group/v1`
- 机器说明书：<https://ai.hengyu.group/agent.md>

## 一句话说明

同一个网页，全世界的 agent 不用各抓一遍。公共网页我们只抓一次，缓存给所有人用。
请求方省 token、省时间，被抓的站点也只被骚扰一次。

## 接入（30 秒）

Claude Code / Cursor / 其他支持远程 MCP 的客户端：

```bash
claude mcp add --transport http ada https://ai.hengyu.group/mcp
```

或写进客户端配置：

```json
{
  "mcpServers": {
    "ada": {
      "type": "http",
      "url": "https://ai.hengyu.group/mcp"
    }
  }
}
```

读类接口**免密钥**；键值存储使用自领密钥（`id_register` 申请）。

## 工具清单（8 个）

| 工具 | 作用 |
| --- | --- |
| `web_read` | 读取网页正文（全站共享缓存，命中即秒回） |
| `text_slice` | 从长文里按关键词取几行，带行号，省 token |
| `clock_now` | 标准时间（UTC+8，已 NTP 校时） |
| `id_attest` | 校验 Ed25519 签名 |
| `memory_put` | 写入键值存储一条 |
| `memory_get` | 从键值存储读取一条 |
| `memory_list` | 列出键值存储条目 |
| `memory_delete` | 删一条 |

键值存储配额：1MB / 200 条 / 单条 64KB。

## 机器名片 / 发现文件

| 文件 | 地址 |
| --- | --- |
| A2A agent card | `https://ai.hengyu.group/.well-known/agent-card.json` |
| MCP server card | `https://ai.hengyu.group/.well-known/mcp/server-card.json` |
| ARD 发现清单 | `https://ai.hengyu.group/.well-known/ard.json` |
| robots | `https://ai.hengyu.group/robots.txt` |
| sitemap | `https://ai.hengyu.group/sitemap.xml` |

## 官方登记

- 官方 MCP 注册表：`group.hengyu.ai/ada`（status: active）
- Smithery：<https://smithery.ai/servers/hengyu/ada>

## 接口边界

- 本站提供 MCP（JSON-RPC 2.0 over HTTP）与 HTTP+JSON 接口，不实现 A2A `message/send` 任务。
- 键值存储的内容由调用方自行写入，按密钥隔离。

## 关于

衡羽科技（Hengyu Technology）· <https://hengyu.group>

Public APIs for AI agents: web reading with a shared nationwide cache, text slicing, standard time, Ed25519 attestation, and a key-value memory store. No signup required.
