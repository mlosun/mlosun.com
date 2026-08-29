---
title: "Pi 包"
description: "Pi-Coding-Agent 中文文档 — Pi 包"
weight: 150
created: 2025-07-16
---

> pi 可以帮助你创建 pi 包。让 pi 打包你的扩展、技能、提示模板或主题。

Pi 包将扩展、技能、提示模板和主题打包在一起，以便通过 npm 或 git 分享。可以在 `package.json` 的 `pi` 键下声明资源，或使用约定目录结构。

## 目录 Table of Contents

- [安装与管理](#install-and-manage)
- [包来源](#package-sources)
- [创建 Pi 包](#creating-a-pi-package)
- [包结构](#package-structure)
- [依赖](#dependencies)
- [包过滤](#package-filtering)
- [启用和禁用资源](#enable-and-disable-resources)
- [作用域与去重](#scope-and-deduplication)

## 安装与管理 Install and Manage

> **安全提醒：** Pi 包拥有完整的系统访问权限。扩展会执行任意代码，技能可以指示模型执行任何操作（包括运行可执行文件）。安装第三方包前请审查源代码。

```bash
pi install npm:@foo/bar@1.0.0
pi install git:github.com/user/repo@v1
pi install https://github.com/user/repo  # raw URLs work too
pi install /absolute/path/to/package
pi install ./relative/path/to/package

pi remove npm:@foo/bar
pi list                     # show installed packages from settings
pi update                   # update pi only
pi update --all             # update pi, update packages, and reconcile pinned git refs
pi update --extensions      # update packages and reconcile pinned git refs only
pi update --self            # update pi only
pi update --self --force    # reinstall pi even if current
pi update npm:@foo/bar      # update one package
pi update --extension npm:@foo/bar
```

以上命令管理 pi 包，`pi update` 可以更新 pi CLI 自身。要卸载 pi，请参阅[快速开始](../从这里开始/快速开始.md#uninstall)。

默认情况下，`install` 和 `remove` 写入用户设置（`~/.pi/agent/settings.json`）。使用 `-l` 可写入项目设置（`.pi/settings.json`）。项目设置可以与团队共享，pi 在项目被信任后会在启动时自动安装缺失的包。

要在不安装的情况下试用包，请使用 `--extension` 或 `-e`。这会安装到临时目录，仅当前运行有效：

```bash
pi -e npm:@foo/bar
pi -e git:github.com/user/repo
```

## 包来源 Package Sources

Pi 在设置和 `pi install` 中支持三种来源类型。

### npm

```
npm:@scope/pkg@1.2.3
npm:pkg
```

- 带版本号的规格会被锁定，包更新（`pi update --extensions`、`pi update --all`）时会跳过。
- 用户安装的包位于 `~/.pi/agent/npm/`。
- 项目安装的包位于 `.pi/npm/`。
- 在 `settings.json` 中设置 `npmCommand` 可将 npm 包查找和安装操作固定到特定的包装命令，例如 `mise` 或 `asdf`。

示例：

```json
{
  "npmCommand": ["mise", "exec", "node@20", "--", "npm"]
}
```

### git

```
git:github.com/user/repo@v1
git:git@github.com:user/repo@v1
https://github.com/user/repo@v1
ssh://git@github.com/user/repo@v1
```

- 不带 `git:` 前缀时，仅接受协议 URL（`https://`、`http://`、`ssh://`、`git://`）。
- 带 `git:` 前缀时，接受简写格式，包括 `github.com/user/repo` 和 `git@github.com:user/repo`。
- HTTPS 和 SSH URL 均受支持。
- SSH URL 会自动使用你配置的 SSH 密钥（遵循 `~/.ssh/config`）。
- 对于非交互式运行（例如 CI），可设置 `GIT_TERMINAL_PROMPT=0` 禁用凭据提示，并设置 `GIT_SSH_COMMAND`（例如 `ssh -o BatchMode=yes -o ConnectTimeout=5`）实现快速失败。
- Refs 是固定的标签或提交。`pi update --extensions` 和 `pi update --all` 不会将它们更新到更新的 ref，但会使现有克隆与配置的 ref 保持一致。
- 使用 `pi install git:host/user/repo@new-ref` 更新设置并将已有包移动到新的固定 ref。
- 克隆到 `~/.pi/agent/git/<host>/<path>`（全局）或 `.pi/git/<host>/<path>`（项目）。
- 当一致性同步更改了检出时，pi 会重置并清理克隆，然后如果存在 `package.json` 则运行 `npm install`。

**SSH 示例：**

```bash
# git@host:path shorthand (requires git: prefix)
pi install git:git@github.com:user/repo

# ssh:// protocol format
pi install ssh://git@github.com/user/repo

# With version ref
pi install git:git@github.com:user/repo@v1.0.0
```

### 本地路径 Local Paths

```
/absolute/path/to/package
./relative/path/to/package
```

本地路径指向磁盘上的文件或目录，会被添加到设置中但不会复制。相对路径相对于其所在的设置文件解析。如果路径是文件，则作为单个扩展加载。如果是目录，pi 按照包规则加载资源。

## 创建 Pi 包 Creating a Pi Package

在 `package.json` 中添加 `pi` 声明，或使用约定目录结构。建议包含 `pi-package` 关键字以提高可发现性。

```json
{
  "name": "my-package",
  "keywords": ["pi-package"],
  "pi": {
    "extensions": ["./extensions"],
    "skills": ["./skills"],
    "prompts": ["./prompts"],
    "themes": ["./themes"]
  }
}
```

路径相对于包根目录。数组支持 glob 模式和 `!` 排除规则。

### 图库元数据 Gallery Metadata

[包图库](https://pi.dev/packages)会展示标记了 `pi-package` 的包。添加 `video` 或 `image` 字段可显示预览：

```json
{
  "name": "my-package",
  "keywords": ["pi-package"],
  "pi": {
    "extensions": ["./extensions"],
    "video": "https://example.com/demo.mp4",
    "image": "https://example.com/screenshot.png"
  }
}
```

- **video**：仅 MP4 格式。桌面端悬停时自动播放，点击打开全屏播放器。
- **image**：PNG、JPEG、GIF 或 WebP 格式。作为静态预览显示。

如果两者都设置，video 优先。

## 包结构 Package Structure

### 约定目录 Convention Directories

如果没有 `pi` 声明，pi 会自动从以下目录发现资源：

- `extensions/` 加载 `.ts` 和 `.js` 文件
- `skills/` 递归查找 `SKILL.md` 文件夹，并将顶层 `.md` 文件作为技能加载
- `prompts/` 加载 `.md` 文件
- `themes/` 加载 `.json` 文件

## 依赖 Dependencies

第三方运行时依赖应放在 `package.json` 的 `dependencies` 中。不注册扩展、技能、提示模板或主题的依赖也放在 `dependencies` 中。当 pi 从 npm 或 git 安装包时，会运行 `npm install`，因此这些依赖会自动安装。

Pi 为扩展和技能绑定了核心包。如果你导入了以下任何包，请将它们列在 `peerDependencies` 中，使用 `"*"` 版本范围，并且不要打包：`@earendil-works/pi-ai`、`@earendil-works/pi-agent-core`、`@earendil-works/pi-coding-agent`、`@earendil-works/pi-tui`、`typebox`。

其他 pi 包必须打包在你的 tarball 中。将它们添加到 `dependencies` 和 `bundledDependencies` 中，然后通过 `node_modules/` 路径引用其资源。Pi 以独立的模块根加载包，因此不同的安装不会冲突或共享模块。

示例：

```json
{
  "dependencies": {
    "shitty-extensions": "^1.0.1"
  },
  "bundledDependencies": ["shitty-extensions"],
  "pi": {
    "extensions": ["extensions", "node_modules/shitty-extensions/extensions"],
    "skills": ["skills", "node_modules/shitty-extensions/skills"]
  }
}
```

## 包过滤 Package Filtering

使用设置中的对象形式过滤包加载的内容：

```json
{
  "packages": [
    "npm:simple-pkg",
    {
      "source": "npm:my-package",
      "extensions": ["extensions/*.ts", "!extensions/legacy.ts"],
      "skills": [],
      "prompts": ["prompts/review.md"],
      "themes": ["+themes/legacy.json"]
    }
  ]
}
```

`+path` 和 `-path` 是相对于包根目录的精确路径。

- 省略某个键则加载该类型的所有资源。
- 使用 `[]` 则不加载该类型任何资源。
- `!pattern` 排除匹配项。
- `+path` 强制包含精确路径。
- `-path` 强制排除精确路径。
- 过滤器在声明之上叠加，进一步缩小已允许的范围。

## 启用和禁用资源 Enable and Disable Resources

使用 `pi config` 启用或禁用已安装包及本地目录中的扩展、技能、提示模板和主题。`pi config` 默认在全局设置（`~/.pi/agent/settings.json`）中启动；按 Tab 键可在全局和项目本地模式间切换。使用 `pi config -l` 可在项目覆盖（`.pi/settings.json`）中启动，继承的全局资源将以灰色显示。

## 作用域与去重 Scope and Deduplication

包可以同时出现在全局和项目设置中。如果同一个包出现在两者中，项目条目优先，除非项目条目设置了 `autoload: false`，此时它作为对全局条目的增量覆盖应用。身份由以下因素决定：

- npm：包名
- git：不带 ref 的仓库 URL
- local：解析后的绝对路径
