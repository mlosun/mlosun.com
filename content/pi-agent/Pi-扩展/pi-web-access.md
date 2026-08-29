---
title: "pi-web-access — 搜索、抓取、看视频"
created: 2026-07-17
description: "多引擎 Web 搜索、URL 内容提取、GitHub 克隆、PDF 解析、YouTube 视频理解，零配置开箱即用。"
tags: ["pi", "extension", "web", "search", "youtube", "github"]
---

## 简介

`pi-web-access` 是 Pi 的上网卡。装了它，Pi 就能搜索网页、抓取文章、克隆 GitHub 仓库、甚至看懂 YouTube 视频。最方便的是——**零配置就能用**，装完即用，什么都不用配。

以前你问 Pi "Rust 最新版本有什么新特性？"，它只能靠训练数据猜。现在它会自动搜一下，给你带上出处的答案。你丢个 GitHub PR 链接过去，它直接把仓库克隆下来分析代码。你贴个 YouTube 教程链接，它能提取视频内容然后回答你的问题。所有这些能力都是 Agent 自己判断时机来调用的，你不需要手动指挥。

## 核心功能

| 功能 | 说明 |
|------|------|
| Web 搜索 | 一次搜索自动尝试 8 个引擎，一个挂了就换下一个。支持按时间（最近一天/一周/月/年）和域名过滤结果 |
| 网页抓取 | 丢个 URL 给 Pi，它抓下来转成 Markdown 直接分析。GitHub 链接会被识别出来，走克隆而不是爬虫。被反爬的页面也有回退方案 |
| 视频理解 | YouTube 和本地视频都能处理。提取字幕、截关键帧、对着画面提问——适合快速消化教程和会议录像 |
| 批量搜索 | 一次提多个搜索角度（`queries`），各自独立搜索后自动汇总，比单次搜索覆盖更广 |
| 搜索策展器 | 搜索结果先在一个浏览器窗口中让你审阅——增删来源、改摘要，批准后才进入对话。不想审的话可以关掉或切到自动模式 |

## 安装配置

### 安装

```bash
pi install npm:pi-web-access

# 可选依赖（视频帧提取）
brew install ffmpeg   # macOS
brew install yt-dlp   # macOS
```

安装完立刻能用——Exa MCP 提供零配置搜索。如果 Pi 已通过 `/login` 登录 Codex，OpenAI 搜索也可以免 key 使用。

### 推荐配置

以下是我的推荐配置，仅保留实用且免费额度充裕的引擎，模型选用 deepseek-v4 兼顾速度和质量。Clash Verge 用户需配置 `ssrf.allowRanges` 解除代理网络限制：

```json
{
  "exaApiKey": "exa-...",
  "tavilyApiKey": "tvly-...",
  "provider": "auto",
  "webSearch": { "enabled": true },
  "chromeProfile": "Default",
  "allowBrowserCookies": true,
  "searchModel": "opencode-go/deepseek-v4-flash",
  "summaryModel": "opencode-go/deepseek-v4-pro",
  "workflow": "auto-summary",
  "youtube": {
    "enabled": true,
    "preferredModel": "gemini-3-flash-preview"
  },
  "video": {
    "enabled": true,
    "preferredModel": "gemini-3-flash-preview"
  },
  "ssrf": {
    "allowRanges": ["198.18.0.0/15"]
  }
}
```

### 搜索引擎

各引擎的简介与免费额度：

| 引擎 | 简介 | 免费额度 | 获取 Key |
|------|------|----------|----------|
| OpenAI | OpenAI 官方搜索 | 无（按量付费，Codex 订阅免费） | [platform.openai.com](https://platform.openai.com/api-keys) |
| Exa | 面向 AI 的语义搜索引擎 | 零配置 1000 次/月<br>配 Key 2 万次/月 | [dashboard.exa.ai](https://dashboard.exa.ai/api-keys) |
| Brave | 注重隐私的搜索引擎 | 1000 次/月（需绑卡） | [api.search.brave.com](https://api.search.brave.com/) |
| Parallel | 低延迟 AI 搜索，适合 Agent 工具调用 | 赠送 $20 一次性额度 | [platform.parallel.ai](https://platform.parallel.ai/) |
| Tavily | 专为 AI Agent 设计的搜索 API | 1000 积分/月 | [app.tavily.com](https://app.tavily.com/) |
| Perplexity | AI 答案引擎，搜索结果直接带引用来源 | 无 | [console.perplexity.ai](https://console.perplexity.ai/) |
| Gemini API | Google 搜索，通过 Gemini API 调用 | 中国大陆不可用（需代理 + 支持地区的 Google 账号） | [aistudio.google.com](https://aistudio.google.com/apikey) |
| Gemini Web | 读取 Chrome 登录 Cookie 免 Key 使用 | 免费（macOS 可用，Windows 实测未成功） | 无需（登录 Google 账号即可） |

### 完整配置说明

#### API Keys

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `openaiApiKey` | — | OpenAI API Key |
| `braveApiKey` | — | Brave Search API Key |
| `exaApiKey` | — | Exa API Key |
| `parallelApiKey` | — | Parallel API Key |
| `tavilyApiKey` | — | Tavily API Key |
| `perplexityApiKey` | — | Perplexity API Key |
| `geminiApiKey` | — | Gemini API Key |

> 获取链接见上方 [搜索引擎](#搜索引擎) 表格。

#### 搜索

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `provider` | `auto` | 默认搜索提供商。可选：`auto`、`openai`、`exa`、`brave`、`parallel`、`tavily`、`perplexity`、`gemini`、`gemini-web` |
| `webSearch.enabled` | `true` | 设为 `false` 禁用 `web_search` 工具 |
| `searchModel` | — | 搜索使用的模型 |
| `summaryModel` | — | 策展器摘要草稿模型 |
| `workflow` | `summary-review` | 搜索工作流：`none`、`summary-review`、`auto-summary` |

#### Gemini 认证

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `chromeProfile` | — | Chromium 配置文件名称，多账号时指定用哪个 |
| `allowBrowserCookies` | `false` | 设为 `true` 允许读取浏览器 Cookie 用于 Gemini Web |
| `geminiBaseUrl` | — | Gemini API 自定义 Base URL |

#### 策展器

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `curatorTimeoutSeconds` | `20` | 策展器超时时间（秒） |
| `shortcuts.curate` | — | 策展器快捷键 |
| `shortcuts.activity` | — | 活动监视器快捷键 |

#### GitHub 克隆

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `githubClone.enabled` | `true` | 是否启用 GitHub 仓库自动克隆 |
| `githubClone.maxRepoSizeMB` | `350` | 最大仓库体积（MB），超过则改用 API 模式 |
| `githubClone.cloneTimeoutSeconds` | `30` | 克隆超时（秒） |
| `githubClone.clonePath` | — | 自定义克隆目录 |

#### 视频

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `youtube.enabled` | `true` | 是否启用 YouTube 视频解析 |
| `youtube.preferredModel` | — | YouTube 解析优先模型 |
| `video.enabled` | `true` | 是否启用本地视频分析 |
| `video.preferredModel` | — | 视频分析优先模型 |
| `video.maxSizeMB` | `50` | 本地视频最大体积（MB） |

#### 网络

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `ssrf.allowRanges` | `[]` | SSRF 白名单（CIDR），Clash Verge 等代理网络需设 `["198.18.0.0/15"]` |
| `cloudflareApiKey` | — | Cloudflare API Key，用于 Workers AI 等场景 |

## 工具、技能与命令

安装 pi-web-access 后，以下能力被注入 Pi：

### 工具

由 Agent 根据任务自主调用，无需手动触发。

| 工具 | 说明 |
|------|------|
| `web_search` | 多引擎 Web 搜索，支持单 `query` 和批量 `queries` |
| `fetch_content` | URL 内容提取、PDF 解析、YouTube 视频解析、GitHub 仓库克隆、本地视频分析 |
| `get_search_content` | 获取之前搜索/提取结果的完整内容（结果超过 30000 字符时会被截断，通过此工具取回全文） |

### 技能

加载后影响 Agent 的行为风格。

| 技能 | 说明 |
|------|------|
| `librarian` | 研究型技能，引导 Agent 以"图书管理员"风格进行源码调研，要求引用代码行和 GitHub 永久链接作为证据 |

### 命令

在对话中直接输入 `/xxx` 手动触发。

| 命令 | 说明 |
|------|------|
| `/websearch` | 直接打开搜索策展器，手动输入搜索词，审阅结果后批准摘要注入对话 |
| `/curator` | 运行时开关策展器：`/curator on` 启用、`/curator off` 关闭 |
| `/search` | 浏览当前会话中所有已存储的搜索结果，及其 `responseId` 便于后续检索 |
| `/google-account` | 显示当前 Gemini Web 认证的 Google 账号 |

## 使用场景

- **调研新技术** — Agent 调用 `web_search` 搜索「Python asyncio 性能优化最佳实践」，返回带来源引用的答案，不用离开终端
- **阅读文档** — 丢给 Pi 一个 `https://pi.dev/docs`，`fetch_content` 抓下来转 Markdown 直接分析，再根据内容回答你的问题
- **审查 PR** — 贴个 GitHub PR 链接，`fetch_content` 自动克隆仓库，Agent 对比代码变更、给出审查意见
- **消化视频** — 粘贴 YouTube 教程链接，Agent 提取转录 + 关键帧截图，你可以追问「他在 5 分钟那里演示的配置具体怎么做？」
- **手动把关** — 输入 `/websearch Python FastAPI 项目结构最佳实践`，在策展器中审阅来源、修改摘要，确认准确后再注入对话

## 参考链接

- [官方仓库](https://github.com/nicobailon/pi-web-access) — 源码、Issue、更新日志
- [pi.dev 包页面](https://pi.dev/packages/pi-web-access) — 官方文档
- [npm 包页面](https://www.npmjs.com/package/pi-web-access) — 版本历史
