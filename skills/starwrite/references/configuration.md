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
| 实时搜索、网页阅读、深度研究 | `UNIFUNCS_API_KEY` | 用户只有选题或要求补充研究时 | UniFuncs 控制台的 API Key 页面 |
| 公众号普通草稿 | `WECHAT_APP_ID`、`WECHAT_APP_SECRET` | 用户确认创建草稿、且文章与图片已通过检查时 | 微信公众平台 → 设置与开发 → 基本配置 |
| 指定外部生图服务 | 对应服务的 API Key | 原生生图失败、用户点名或批量生成时 | 对应服务控制台 |

向导还要提醒公众号用户：在微信公众平台为 API 调用机器配置 IP 白名单；本地运行时填当前出口公网 IP。App Secret 只展示给用户自己，不在对话或日志中回显。

## 写入格式

```dotenv
UNIFUNCS_API_KEY=...
WECHAT_APP_ID=...
WECHAT_APP_SECRET=...
```

每次写入前说明：仅保存到本机私有目录，不进入文章项目、Git、Vercel 或公开 Skill。允许用户跳过某项能力：跳过搜索则只能使用已给素材；跳过公众号配置则生成 HTML 和本地预览，不创建草稿。
