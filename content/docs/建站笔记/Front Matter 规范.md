---
created: 2026-07-11
title: Front Matter 规范
tags: [Hugo, 规范]
---

## 本站约定

- `title` 是唯一必填字段，没有它 Hugo 无法渲染页面。
- 日期使用本站自定义的 `created` / `updated` 字段，由 `hugo.yml` 的 `frontmatter` 映射到 Hugo 原生的 `date` / `lastmod`。
- `updated` 一般不写——页面「最后修改」由 `:git`（最后一次 commit 时间）自动填充；手动写 `updated` 才会覆盖。
- 编辑器可能对 `created` / `updated` 报 schema 错误，这是误报，勿改名（改名会破坏日期映射）。
- 字段取值优先级：页面 front matter → 上级 `cascade` → `hugo.yml` 站点参数。

## 文章页字段

| 字段 | 作用 |
| --- | --- |
| `title` | **必需**。页面标题，Hugo 没有它无法渲染页面 |
| `created` | 创建日期，`YYYY-MM-DD`，页面显示为「创建于」 |
| `tags` | 标签数组，如 `[Docker, NAS]`，Hugo 自动生成标签页 |
| `description` | 页面描述，用于搜索结果与社交分享；不写则自动截取正文前 70 字 |
| `updated` | 手动指定更新日期；不写则由最后一次 commit 时间自动填充 |
| `weight` | 列表内排序，数字越小越靠前 |
| `draft` | `true` 则页面不构建，`hugo server --buildDrafts` 下可见 |

## OINK 主题常用字段

| 字段 | 作用 |
| --- | --- |
| `linkTitle` | 短标题，用于侧栏、面包屑与翻页器（正文大标题不变） |
| `icon` | Font Awesome 图标类（如 `fa-solid fa-rocket`），用于侧栏与栏目卡片 |
| `categories` | 分类，与 `tags` 同为 taxonomy，Hugo 自动生成分类页 |
| `series` | 系列名；`series_weight` 决定在系列中的顺序 |
| `images` | 封面图，第一项兼作社交分享卡片 |
| `sidebar_enabled` | `false` 隐藏左侧栏，独立页面常用 |
| `breadcrumb` | `false` 隐藏面包屑导航；博客分区默认关，其余默认开 |
| `notoc` | `true` 则不显示右栏页面目录 |
| `pager` | `false` 关闭本页底部上一页/下一页 |
| `backlinks` | `false` 隐藏右栏「反链」组（本站已在站点级开启） |
| `comments` | 评论开关：站点级 giscus 已启用，单页写 `comments: false` 可关闭 |
| `aliases` | 旧路径重定向到本页，页面改名时用于保住旧评论串与外链 |

完整字段表见 [OINK Front Matter 官方文档](https://oink.pgsty.com/zh/docs/write/frontmatter/)。

## 文章页模板

```yaml
---
created: YYYY-MM-DD
title: 本文标题
tags: [标签1, 标签2]
---
```

## 索引页

各分区的 `_index.md`：

| 字段 | 作用 |
| --- | --- |
| `title` | **必需**。分区名称 |
| `description` | 分区描述，用于栏目卡片与 SEO |
| `weight` | 该分区在父级列表中的排序 |

## 日期机制

`hugo.yml` 中配置了 front matter 的取值优先级：

```yaml
frontmatter:
  date:
    - created       # 第一优先级 → 页面创建日期
    - date
  lastmod:
    - updated       # 手动覆盖（可选）
    - :git          # 由 Git 提交记录自动填充
    - lastmod
```

- **创建日期**：Hugo 从 `created` 取值。
- **更新日期**：默认由 `:git` 自动填充（最后一次 commit 时间），如需手动指定，在 front matter 中写 `updated` 即可覆盖。
