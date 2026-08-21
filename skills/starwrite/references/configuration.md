# 配置向导

Starwrite 只在某项能力将要使用时询问相应配置。不要要求用户先读 README 手工配完所有 Key。

## 私有配置位置

默认使用：

```text
~/.config/starwrite/.env
```

文件权限应仅限当前用户读取，例如 macOS/Linux 使用 `chmod 600 ~/.config/starwrite/.env`。该文件必须在 Git 忽略范围之外。

## 按需询问

| 能力 | 所需配置 | 何时询问 | 获取位置 |
| --- | --- | --- | --- |
| 实时搜索、网页阅读、深度搜索、深度研究 | `UNIFUNCS_API_KEY`（同一把 Key） | 用户只有选题或要求补充研究时 | UniFuncs 控制台的 API Key 页面 |
| 公众号普通草稿 | `WECHAT_APP_ID`、`WECHAT_APP_SECRET` | 用户确认创建草稿、且文章与图片已通过检查时 | 微信公众平台 → 设置与开发 → 基本配置 |
| 指定外部生图服务 | 对应服务的 API Key | 原生生图失败、用户点名或批量生成时 | 对应服务控制台 |

向导还要提醒公众号用户：在微信公众平台为 API 调用机器配置 IP 白名单；本地运行时填当前出口公网 IP。App Secret 只展示给用户自己，不在对话或日志中回显。

## UniFuncs 模式选择

一把 `UNIFUNCS_API_KEY` 可调用四种能力；不需要为不同模式配置不同变量。根据任务自动选择，用户也可在自然语言中明确指定：

| 模式 | 用途 | 典型指令 |
| --- | --- | --- |
| 实时搜索 `web-search` | 找最新信息、官方入口和候选来源 | “用实时搜索查今天的更新” |
| 网页阅读 `web-reader` | 读取已选中的网页、PDF 或文档原文 | “读这篇文章并提取事实” |
| 深度搜索 `deepsearch` | 需要更广的多源搜索与来源清单 | “用深度搜索比较这几个方案” |
| 深度研究 `deepresearch` | 需要研究推理、反例和较完整报告的复杂选题 | “用深度研究做一份行业分析” |

同一 Key 下各能力独立计费或受账户权限限制。若 API 返回无权限或余额不足，提示用户检查 UniFuncs 账户权限与余额，不要求更换 Key。

## REST API 调用速查

Starwrite 不携带 UniFuncs HTTP 客户端，也不强制要求安装 UniFuncs MCP。若运行 Agent 具备 HTTPS 请求能力，优先直接调用官方 REST 端点：

| 能力 | 方法 | 端点 | 关键参数 / 说明 |
| --- | --- | --- | --- |
| 实时搜索 | POST | `https://api.unifuncs.com/api/web-search/search` | `query`、`count`；返回 `code==0` 与 `data.webPages`。 |
| 网页阅读 | POST | `https://api.unifuncs.com/api/web-reader` | `url`、`format: markdown`；成功时返回 Markdown 正文（非 JSON 包装）。 |
| 深度搜索 | POST | `https://api.unifuncs.com/deepsearch/v1/chat/completions` | OpenAI 兼容格式，`model: s3`，`messages: [...]`。 |
| 深度研究-创建任务 | POST | `https://api.unifuncs.com/deepresearch/v1/create_task` | 必须使用 `messages` 字段；`max_depth` 最小值为 `2`；`model` 可选 `u3` 等。 |
| 深度研究-查询任务 | GET | `https://api.unifuncs.com/deepresearch/v1/query_task?task_id=...` | 轮询至 `status` 为 `completed`。 |

统一认证头：`Authorization: Bearer <UNIFUNCS_API_KEY>`，`Content-Type: application/json`。

> 经验：直接 REST 调用无需额外依赖，适合 Starwrite 这类纯 Markdown 编排层 Skill；只有当 Agent 本身无法发起 HTTPS 请求时，才需要连接 UniFuncs MCP 作为兜底。

Starwrite 不携带 UniFuncs HTTP 客户端。运行 Agent 必须已连接 UniFuncs MCP，或能经官方 REST API 发起 HTTPS 请求。两者均不可用时，先报告该前提未满足。

## 写入格式

```dotenv
UNIFUNCS_API_KEY=...
WECHAT_APP_ID=...
WECHAT_APP_SECRET=...
```

每次写入前说明：仅保存到本机私有目录，不进入文章项目、Git、Vercel 或公开 Skill。允许用户跳过某项能力：跳过搜索则只能使用已给素材；跳过公众号配置则生成 HTML 和本地预览，不创建草稿。
