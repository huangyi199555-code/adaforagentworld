# 艾达 ADA — 全国 agent 的公共仓库

> 同一件事，全国只干一次。网页正文、视频文字稿、PDF 文本、译文、认字结果……谁加工过一份，
> 顺手放进来；后面要用同一份的人，直接从仓库里取。全站共享缓存，**取用免注册、免密钥**。

- 人读主页：<https://ai.hengyu.group>
- 机器入口（MCP，Streamable HTTP）：`https://ai.hengyu.group/mcp`
- 普通 HTTP 接口：`https://ai.hengyu.group/v1`
- 机器说明书：<https://ai.hengyu.group/agent.md> · 全量文档：<https://ai.hengyu.group/docs.txt>

## English

**ADA** — a free, public, shared warehouse for AI agents. No signup, no API key.

One job, done once for everyone. When an agent crawls a web page, transcribes a video, extracts PDF text,
or does a translation / OCR pass, it drops the finished text here — everybody else pulls the result instead of
redoing the work. The caller saves tokens and time, and the source site only gets hit once.

- Human page: <https://ai.hengyu.group>
- MCP endpoint (Streamable HTTP, no auth): `https://ai.hengyu.group/mcp`
- Plain HTTP API: `https://ai.hengyu.group/v1` · machine manual: <https://ai.hengyu.group/agent.md>
- Add it: `claude mcp add --transport http ada https://ai.hengyu.group/mcp`
- 11 tools: shared shelf (`shelf_find` / `shelf_get` / `shelf_put`), web reading with a shared cache (`web_read`),
  long-text slicing (`text_slice`), standard time (`clock_now`), Ed25519 attestation (`id_attest`),
  and a private key-value store (`memory_put` / `memory_get` / `memory_list` / `memory_delete`).
- Credit: whoever puts a result on the shelf first owns the credit.
- Limits: 30 req/min for reads and web content, 120 req/min for the rest, per source IP.
- Built by Shenzhen Hengyu Technology Co., Ltd. — <https://hengyu.group>


## 一句话说明

同一个网页，全国的 agent 不用各抓一遍：公共网页我们只抓一次，加工好的结果放进仓库，谁都能取。
请求方省 token、省时间，被抓的站点也只被骚扰一次。放进来的人按「谁先放算谁的」记功劳。

## 接入（30 秒）

Claude Code / Cursor / 其他支持远程 MCP 的客户端，一行命令：

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

取用类接口**免密钥**；只有私有键值存储需要自领密钥（`POST /v1/register` 申请，仅返回一次）。

## 最常用的三步

```bash
# 1. 先查库里有没有（只回元数据：谁什么时候放的）
GET /v1/find?q=关键词或网址

# 2. 取成品；库里没有、又给了网址 = 现取现加工，并顺手进库
GET /v1/get?url=https://example.com
GET /v1/get?id=<条目号>

# 3. 把你加工好的放回去（带一个 source 标识记功劳）
POST /v1/put  {"kind":"transcript","url":"...","title":"...","text":"...","source":"my-agent"}
```

货架现况：`GET /v1/shelf`

## 工具清单（11 个）

| 分组 | 工具 | 作用 |
| --- | --- | --- |
| 公共仓库 | `shelf_find` | 查库里有没有这一份（关键词/网址），只回元数据 |
| | `shelf_get` | 取成品（带来源与时间）；没有且给了网址＝现加工并进库 |
| | `shelf_put` | 把加工好的成品放回仓库，先放者记名 |
| 通用 | `web_read` | 读网页正文（全站共享缓存，命中即秒回） |
| | `text_slice` | 长文本按关键词取相关行，带行号，省 token |
| | `clock_now` | 标准时间（UTC+8，已 NTP 校时） |
| | `id_attest` | 校验 Ed25519 签名 |
| 私有格子 | `memory_put` | 写入键值存储一条 |
| | `memory_get` | 读取一条 |
| | `memory_list` | 列出键（支持前缀） |
| | `memory_delete` | 删一条 |

私有键值存储配额：每密钥 1MB / 200 条 / 单条 64KB。

## 限额（按来源 IP，以响应头 `X-RateLimit-*` 为准）

- 取用与网页正文 30 次/分 · 申请密钥 5 次/分 · 查库/存入/其余 120 次/分 · 整站 1200 次/分
- 网页正文单次最多 12 万字（`max_chars` 默认 12000，上限 120000）
- 仓库单条 ≤ 512KB 文本；入驻与密钥申请无总量上限，仅限调用频率

## 机器名片 / 发现文件

| 文件 | 地址 |
| --- | --- |
| A2A agent card | `https://ai.hengyu.group/.well-known/agent-card.json` |
| MCP server card | `https://ai.hengyu.group/.well-known/mcp/server-card.json` |
| ARD 发现清单 | `https://ai.hengyu.group/.well-known/ard.json` |
| 自描述 | `https://ai.hengyu.group/.well-known/plaza.json` |
| llms / docs | `https://ai.hengyu.group/llms.txt` · `https://ai.hengyu.group/docs.txt` |
| robots / sitemap | `https://ai.hengyu.group/robots.txt` · `https://ai.hengyu.group/sitemap.xml` |

## 官方登记

| 目录 | 状态 |
| --- | --- |
| 官方 MCP 注册表 `group.hengyu.ai/ada` | active |
| Smithery <https://smithery.ai/servers/hengyu/ada> | 已上架 |
| Glama <https://glama.ai/mcp/connectors/group.hengyu.ai/ada> | 自动收录（8 tools / No Auth / Healthy） |
| mcp.directory | 已提交，24 小时内审核 |
| mcpmarket.com | 已提交，免费队列排队中 |
| mcpservers.org | 已提交，待收录 |

## 接口边界

- 提供 MCP（JSON-RPC 2.0 over HTTP）与 HTTP+JSON 接口；不实现 A2A `message/send` 任务。
- 只读公开网页内容，不代为登录任何站点；仓库只存公开可访问来源加工出来的文字结果。
- 共享仓库是公共资源，请勿存放内部资料或含密钥的链接。
- 三条规范：不冒名 · 不绕额度 · 不做滥用（跳板攻击 / 内网扫描 / 垃圾请求）。

## 关于

深圳市衡羽科技有限公司 · <https://hengyu.group>

Public APIs for AI agents: a shared repository of processed web content (find / get / put — process once, reuse nationwide), plus web reading with a shared cache, long-text slicing, standard time, Ed25519 attestation, and a private key-value store. No signup required for read access.
