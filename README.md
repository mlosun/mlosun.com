# mlosun.com

个人数字花园，基于 [Hugo](https://gohugo.io/) 和 [OINK](https://github.com/pgsty/oink) 主题构建，通过 CNB CI/CD 自动部署到 EdgeOne Pages。

## 项目结构

```text
.
├── hugo.yml                              # Hugo 站点配置（导航、主题参数、搜索、输出格式等）
├── go.mod                                # Go 模块定义，引入 OINK 主题依赖
├── go.sum                                # Go 模块校验和（自动生成，勿手动编辑）
├── .cnb.yml                              # CNB CI/CD 流水线（Hugo 构建 → EdgeOne 部署 → GitHub 镜像同步）
├── .gitignore                            # Git 忽略规则（构建产物、资源缓存等）
├── content/                              # 站点内容目录（Markdown，分区与命名随内容演进）
├── data/                                 # 主题数据文件
│   ├── home.yaml                         # 首页落地页数据（OINK landing 模型）
│   └── footer.yaml                       # 页脚多列链接数据（OINK fat footer）
└── static/                               # 静态资源（favicon、图片、EdgeOne 响应头配置）
```

构建产物 `public/`、资源缓存 `resources/` 与锁文件 `.hugo_build.lock` 本地存在但不纳入版本管理。

## 关键文件

| 文件 | 作用 |
| --- | --- |
| `hugo.yml` | 站点核心配置：URL、导航菜单、OINK 主题参数（本地搜索、亮暗模式、代码高亮、图片缩放等） |
| `go.mod` | 声明 Hugo 模块依赖（OINK），Hugo 通过 Go module 机制加载主题 |
| `go.sum` | 依赖校验和文件，`hugo mod get` / `hugo mod tidy` 自动更新 |
| `.cnb.yml` | CNB 平台 CI/CD：推送 main 分支时自动 `hugo --minify` 构建并部署到 EdgeOne Pages，同时经 `include` 的 Sync-to-Github 流水线同步到 [GitHub 镜像](https://github.com/mlosun/mlosun.com) |
| `data/home.yaml` | 首页落地页数据，首页内容唯一来源 |
| `data/footer.yaml` | 页脚多列链接，全站页脚唯一来源 |
| `static/edgeone.json` | EdgeOne Pages 响应头配置：给 `/css/*` 加 CORS 头，giscus 跨域读取评论配色 CSS 依赖它，误删会导致评论样式回退 |

## 本地预览

安装 Hugo extended 版本（v0.160.1+，OINK 主题要求），然后：

```bash
hugo server --buildDrafts --disableFastRender
```

浏览 http://localhost:1313 即可预览。

常用参数说明：

| 参数 | 作用 |
| --- | --- |
| `--buildDrafts`（`-D`） | 连 `draft: true` 的草稿一起构建，写作期间可见未发布内容 |
| `--disableFastRender` | 关闭增量渲染，每次改动全量重建。略慢，但配置、短代码这类改动不会漏更新 |
| `--navigateToChanged`（`-N`） | 保存文件后，浏览器自动跳转到刚修改的页面，适合长文写作 |
| `--buildFuture`（`-F`） | 连发布日期在未来的文章一起构建 |
| `--port 1314`（`-p`） | 指定端口（默认 1313），端口被占用时使用 |
| `--openBrowser`（`-O`） | server 启动后自动打开浏览器 |
| `--noHTTPCache` | 禁用浏览器缓存，预览始终看到最新页面 |
| `--tlsAuto` | 本地 HTTPS 预览（自动生成证书），调试需要安全上下文的功能时使用 |
| `--noBuildLock` | 不创建 `.hugo_build.lock`，可与另一个终端的 `hugo` 构建并行不互堵 |
| `--panicOnWarning` | 遇到第一条 WARNING 就中止，排查模板和配置问题时用 |
| `--poll 700ms` | 改用轮询监听文件变化，文件监听失效（如网络盘）时兜底 |

## 内容写作

文章页 front matter 常用字段（完整说明见站内笔记《Front Matter 规范》）：

| 字段 | 作用 |
| --- | --- |
| `title` | **必需**。页面标题，Hugo 没有它无法渲染页面 |
| `created` | 创建日期，`YYYY-MM-DD`，页面显示为「创建于」 |
| `tags` | 标签数组，如 `[Docker, NAS]`，Hugo 自动生成标签页 |
| `description` | 页面描述，用于搜索结果与社交分享；不写则自动截取正文前 70 字 |
| `updated` | 手动指定更新日期；不写则由最后一次 commit 时间自动填充 |
| `weight` | 列表内排序，数字越小越靠前 |
| `draft` | `true` 则页面不构建 |

> `created` / `updated` 是本站自定义字段，由 `hugo.yml` 的 `frontmatter` 映射到 Hugo 原生的 `date` / `lastmod`。

OINK 主题提供的常用页面字段：

| 字段 | 作用 |
| --- | --- |
| `linkTitle` | 短标题，用于侧栏、面包屑与翻页器（正文大标题不变） |
| `icon` | Font Awesome 图标类（如 `fa-solid fa-rocket`），用于侧栏与栏目卡片 |
| `categories` | 分类，与 `tags` 同为 taxonomy，Hugo 自动生成分类页 |
| `series` | 系列名；`series_weight` 决定在系列中的顺序 |
| `images` | 封面图，第一项兼作社交分享卡片 |
| `sidebar_enabled` | `false` 隐藏左侧栏，独立页面常用 |
| `breadcrumb` | `false` 隐藏面包屑导航；博客分区默认关，其余默认开|
| `notoc` | `true` 则不显示右栏页面目录 |
| `pager` | `false` 关闭本页底部上一页/下一页 |
| `backlinks` | `false` 隐藏右栏「反链」组|
| `comments` | 评论开关：站点级 giscus 已启用，单页写 `comments: false` 可关闭 |

字段取值优先级：页面 front matter → 上级 `cascade` → `hugo.yml` 站点参数；完整字段表见下方「参考链接」中 OINK 的 Front Matter 文档。

文章页模板：

```yaml
---
created: YYYY-MM-DD
title: 本文标题
tags: [标签1, 标签2]
---
```

## 版本升级

### 升级部署版本（CI/CD）

**Hugo**：修改 `.cnb.yml` 中的 Docker 镜像标签（需 ≥ v0.160.1）：

```yaml
image: ghcr.io/gohugoio/hugo:v0.165.0
```

**OINK 主题**：修改 `go.mod` 中的版本号：

```text
require github.com/pgsty/oink v0.8.1
```

### 升级本地版本

本地版本需与部署版本保持一致，避免本地预览与线上效果不同。

```bash
# Hugo（macOS Homebrew）
brew upgrade hugo

# OINK 主题：升级到最新版
hugo mod get -u github.com/pgsty/oink
hugo mod tidy

# 验证
hugo server --buildDrafts --disableFastRender
```

`hugo mod get` 会自动更新 `go.mod` 与 `go.sum`，记得一并提交到 Git。

## 参考链接

| 资源 | 链接 |
| --- | --- |
| Hugo 官方文档 | https://gohugo.io/documentation/ |
| OINK 主题主页 | https://oink.pgsty.com/ |
| OINK 使用文档（中文） | https://oink.pgsty.com/zh/docs/ |
| OINK Front Matter 全表 | https://oink.pgsty.com/zh/docs/write/frontmatter/ |
| OINK 站点配置文档 | https://oink.pgsty.com/zh/docs/customize/ |
| 版本发布 | [Hugo Releases](https://github.com/gohugoio/hugo/releases) / [OINK Releases](https://github.com/pgsty/oink/tags) |
