---
title: "使用 Pi"
description: "Pi-Coding-Agent 中文文档 — 使用 Pi"
weight: 30
created: 2025-07-16
---

本页面收集不适用于快速入门页面的日常使用细节。

## 交互模式 Interactive Mode

<p align="center"><img src="/images/pi-agent/interactive-mode.png" alt="交互模式" width="600"></p>

界面包含四个主要区域：

- **启动头部（Startup header）** — 快捷键、已加载的上下文文件、提示模板、技能和扩展
- **消息区（Messages）** — 用户消息、助手回复、工具调用、工具结果、通知、错误和扩展 UI
- **编辑器（Editor）** — 输入区域；边框颜色表示当前的思考级别
- **底部栏（Footer）** — 工作目录、会话名称、令牌/缓存用量、费用、上下文使用情况和当前模型

编辑器可以临时被内置 UI（如 `/settings`）或自定义扩展 UI 替换。

### 编辑器功能 Editor Features

| 功能 | 操作方式 |
| ------ | ---------- |
| 文件引用 | 输入 `@` 进行项目文件模糊搜索 |
| 路径补全 | 按 Tab 补全路径 |
| 多行输入 | Shift+Enter，或在 Windows Terminal 中使用 Ctrl+Enter |
| 复制回复 | Ctrl+X 复制最后一条助手消息；在 `/tree` 中复制选中的消息 |
| 图片 | 使用 Ctrl+V 粘贴，Windows 上使用 Alt+V，或拖入终端 |
| Shell 命令 | `!command` 执行命令并将输出发送给模型 |
| 隐藏 Shell 命令 | `!!command` 执行命令但不将输出发送给模型 |
| 外部编辑器 | Ctrl+G 打开 `externalEditor`、`$VISUAL`、`$EDITOR`、Windows 上的记事本，或其他平台上的 `nano` |

快捷键和自定义配置请参见[键盘快捷键](快捷键.md)。

## 斜杠命令 Slash Commands

在编辑器中输入 `/` 可打开命令补全。扩展可以注册自定义命令，技能以 `/skill:name` 形式使用，提示模板通过 `/templatename` 展开。

| 命令 | 描述 |
| ------ | ------ |
| `/login`, `/logout` | 管理 OAuth 或 API 密钥凭证 |
| `/model` | 切换模型 |
| `/scoped-models` | 启用/禁用用于 Ctrl+P 循环切换的模型 |
| `/settings` | 思考级别、主题、消息投递方式、传输层 |
| `/resume` | 从之前的会话中选择 |
| `/new` | 开始新会话 |
| `/name <name>` | 设置会话显示名称 |
| `/session` | 显示会话文件、ID、消息、令牌和费用 |
| `/tree` | 跳转到会话中的任意位置并从那里继续 |
| `/trust` | 保存项目信任决策以供未来会话使用 |
| `/fork` | 从之前的用户消息创建新会话 |
| `/clone` | 将当前活动分支复制到新会话中 |
| `/compact [prompt]` | 手动压缩上下文，可附带自定义说明 |
| `/copy` | 将最后一条助手消息复制到剪贴板 |
| `/export [file]` | 将会话导出为 HTML 或 JSONL |
| `/import <file>` | 从 JSONL 文件导入并恢复会话 |
| `/share` | 上传为私有 GitHub Gist，附带可分享的 HTML 链接 |
| `/reload` | 重新加载键盘快捷键、扩展、技能、提示、主题和上下文文件 |
| `/hotkeys` | 显示所有键盘快捷键 |
| `/changelog` | 显示版本历史 |
| `/quit` | 退出 pi |

## 消息队列 Message Queue

你可以在 agent 仍在工作时提交消息：

- **Enter** 将引导消息（steering message）加入队列，在当前助手回合的工具调用完成后投递。
- **Alt+Enter** 将后续消息（follow-up message）加入队列，在 agent 完成所有工作后投递。
- **Escape** 中止操作并将队列中的消息恢复到编辑器中。
- **Alt+Up** 将队列中的消息取回到编辑器中。

在 Windows Terminal 上，Alt+Enter 默认为全屏快捷键。如果你希望 pi 接收该快捷键，请按照[终端设置](../平台设置/终端设置.md)中的说明重新映射。

在[设置](设置.md)中使用 `steeringMode` 和 `followUpMode` 配置投递行为。

## 会话 Sessions

会话自动保存在 `~/.pi/agent/sessions/` 目录中，按工作目录组织。

```bash
pi -c                  # 继续最近的会话
pi -r                  # 浏览并选择会话
pi --no-session        # 临时模式；不保存
pi --name "my task"    # 启动时设置会话显示名称
pi --session <path|id> # 使用特定的会话文件或会话 ID
pi --fork <path|id>    # 将会话分支到新的会话文件
```

有用的会话命令：

- `/session` 显示当前会话文件和 ID。
- `/tree` 导航文件内的会话树，并可汇总已废弃的分支。
- `/fork` 从较早的用户消息创建新会话。
- `/clone` 将当前活动分支复制到新的会话文件中。
- `/compact` 汇总较早的消息以释放上下文空间。

详情请参见[会话](会话.md)和[上下文压缩](上下文压缩.md)。

## 上下文文件 Context Files

Pi 启动时从以下位置加载 `AGENTS.md` 或 `CLAUDE.md`：

- `~/.pi/agent/AGENTS.md` — 全局指令
- 从当前工作目录向上逐级查找的父目录
- 当前目录

使用上下文文件来定义项目约定、命令、安全规则和偏好。使用 `--no-context-files` 或 `-nc` 禁用加载。

### 系统提示文件 System Prompt Files

替换默认系统提示：

- 项目级：`.pi/SYSTEM.md`
- 全局级：`~/.pi/agent/SYSTEM.md`

在以上任一位置使用 `APPEND_SYSTEM.md` 可在不替换默认提示的情况下追加内容。

### 项目信任 Project Trust

交互式启动时，如果项目目录包含本地设置、资源或 `.agents/skills`，且 `~/.pi/agent/trust.json` 中对该目录或其父目录无已保存的信任决策，pi 会询问是否信任该项目。信任后，pi 可加载 `.pi/settings.json` 和 `.pi` 资源、安装缺失的包以及执行项目扩展。

信任决策做出前，pi 仅加载上下文文件、用户/全局扩展和 CLI `-e` 扩展，以便它们处理 `project_trust` 事件。项目本地扩展、包管理的扩展和项目设置仅在信任后才加载。此规则同样适用于切换到来自不同工作目录且当前进程尚未解决信任的会话。

非交互模式（`-p`、`--mode json`、`--mode rpc`）不弹出信任提示。此时使用全局 `defaultProjectTrust`：`ask`（默认）和 `never` 忽略项目资源，`always` 信任资源。可用 `--approve`/`-a` 或 `--no-approve`/`-na` 单次覆盖。

如果没有扩展或已保存的决策适用，则使用 `defaultProjectTrust` 作为回退行为。在 `~/.pi/agent/settings.json` 中将其设置为 `"ask"`、`"always"` 或 `"never"`，或通过 `/settings` 更改。

`pi config` 和包命令使用相同的项目信任流程，但 `pi update` 从不提示。传递 `--approve` 以信任单条命令的项目本地设置，或传递 `--no-approve` 以忽略它们。

在交互模式中使用 `/trust` 保存项目信任决策以供未来会话使用，包括对直接父文件夹的信任。该命令仅写入 `~/.pi/agent/trust.json`；当前会话不会重新加载，因此请重启 pi 以使更改生效。

## 导出与分享会话 Exporting and Sharing Sessions

使用 `/export [file]` 将会话写入 HTML。

使用 `/share` 上传为私有 GitHub Gist，附带可分享的 HTML 链接。

如果你在开源项目中使用 pi，并希望发布会话以供模型、提示、工具和评估研究使用，请参见 [`badlogic/pi-share-hf`](https://github.com/badlogic/pi-share-hf)。该工具将会话发布到 Hugging Face 数据集。

## CLI 参考 CLI Reference

```bash
pi [options] [@files...] [messages...]
```

### 包命令 Package Commands

```bash
pi install <source> [-l]     # 安装包，-l 表示项目本地安装
pi remove <source> [-l]      # 移除包
pi uninstall <source> [-l]   # remove 的别名
pi update [source|self|pi]   # 仅更新 pi，或更新一个包源
pi update --all              # 更新 pi 和所有包；协调锁定的 git 引用
pi update --extensions       # 仅更新包；协调锁定的 git 引用
pi update --self             # 仅更新 pi
pi update --extension <src>  # 更新单个包
pi list                      # 列出已安装的包
pi config                    # 启用/禁用包资源
```

这些命令管理 pi 包，`pi update` 可以更新 pi CLI 安装本身。卸载 pi 请参见[快速入门](快速开始.md#uninstall)。`pi config` 和项目包命令接受 `--approve`/`--no-approve` 以信任或忽略单条命令的项目本地设置。`pi update` 从不提示项目信任。

包源和安全说明请参见[Pi 包](../自定义/pi-包.md)。

### 模式 Modes

| 标志 | 描述 |
| ------ | ------ |
| 默认 | 交互模式 |
| `-p`, `--print` | 打印回复并退出 |
| `--mode json` | 将所有事件输出为 JSON 行；参见 [JSON 模式](../程序化使用/json-事件流模式.md) |
| `--mode rpc` | 通过 stdin/stdout 的 RPC 模式；参见 [RPC 模式](../程序化使用/rpc-模式.md) |
| `--export <in> [out]` | 将会话导出为 HTML |

在打印模式下，pi 也会读取通过管道传入的 stdin 并将其合并到初始提示中：

```bash
cat README.md | pi -p "总结这段文本"
```

### 模型选项 Model Options

| 选项 | 描述 |
| ------ | ------ |
| `--provider <name>` | 提供商，如 `anthropic`、`openai` 或 `google` |
| `--model <pattern>` | 模型模式或 ID；支持 `provider/id` 和可选的 `:<thinking>` |
| `--api-key <key>` | API 密钥，覆盖环境变量 |
| `--thinking <level>` | `off`、`minimal`、`low`、`medium`、`high`、`xhigh`、`max` |
| `--models <patterns>` | 用于 Ctrl+P 循环切换的逗号分隔模式列表 |
| `--list-models [search]` | 列出可用模型 |

### 会话选项 Session Options

| 选项 | 描述 |
| ------ | ------ |
| `-c`, `--continue` | 继续最近的会话 |
| `-r`, `--resume` | 浏览并选择会话 |
| `--session <path\|id>` | 使用特定的会话文件或部分 UUID |
| `--fork <path\|id>` | 将会话文件或部分 UUID 分支到新会话 |
| `--session-dir <dir>` | 自定义会话存储目录 |
| `--no-session` | 临时模式；不保存 |
| `--name <name>`, `-n <name>` | 启动时设置会话显示名称 |

### 工具选项 Tool Options

| 选项 | 描述 |
| ------ | ------ |
| `--tools <list>`, `-t <list>` | 仅允许指定的内置、扩展和自定义工具 |
| `--exclude-tools <list>`, `-xt <list>` | 禁用指定的内置、扩展和自定义工具 |
| `--no-builtin-tools`, `-nbt` | 禁用内置工具但保留扩展/自定义工具 |
| `--no-tools`, `-nt` | 禁用所有工具 |

内置工具：`read`、`bash`、`edit`、`write`、`grep`、`find`、`ls`。

### 资源选项 Resource Options

| 选项 | 描述 |
| ------ | ------ |
| `-e`, `--extension <source>` | 从路径、npm 或 git 加载扩展；可重复使用 |
| `--no-extensions` | 禁用扩展发现 |
| `--skill <path>` | 加载技能；可重复使用 |
| `--no-skills` | 禁用技能发现 |
| `--prompt-template <path>` | 加载提示模板；可重复使用 |
| `--no-prompt-templates` | 禁用提示模板发现 |
| `--theme <path>` | 加载主题；可重复使用 |
| `--no-themes` | 禁用主题发现 |
| `--no-context-files`, `-nc` | 禁用 `AGENTS.md` 和 `CLAUDE.md` 发现 |

将 `--no-*` 与显式标志结合使用，可以精确加载所需内容，忽略设置。示例：

```bash
pi --no-extensions -e ./my-extension.ts
```

### 其他选项 Other Options

| 选项 | 描述 |
| ------ | ------ |
| `--system-prompt <text>` | 替换默认提示；上下文文件和技能仍会附加 |
| `--append-system-prompt <text>` | 追加到系统提示 |
| `--verbose` | 强制显示详细启动信息 |
| `-a`, `--approve` | 信任本次运行的项目本地文件 |
| `-na`, `--no-approve` | 忽略本次运行的项目本地文件 |
| `-h`, `--help` | 显示帮助 |
| `-v`, `--version` | 显示版本 |

### 文件参数 File Arguments

使用 `@` 前缀将文件包含到消息中：

```bash
pi @prompt.md "回答这个问题"
pi -p @screenshot.png "这张图片里有什么？"
pi @code.ts @test.ts "审查这些文件"
```

### 示例 Examples

```bash
# 交互模式，附带初始提示
pi "列出 src/ 中所有 .ts 文件"

# 非交互模式
pi -p "总结这个代码库"

# 非交互模式，通过管道传入 stdin
cat README.md | pi -p "总结这段文本"

# 命名的一次性会话
pi --name "发布审核" -p "审核此仓库"

# 使用不同模型
pi --provider openai --model gpt-4o "帮我重构代码"

# 带提供商前缀的模型
pi --model openai/gpt-4o "帮我重构代码"

# 带思考级别简写的模型
pi --model sonnet:high "解决这个复杂问题"

# 限制模型循环切换范围
pi --models "claude-*,gpt-4o"

# 只读模式
pi --tools read,grep,find,ls -p "审查代码"

# 禁用某个扩展或内置工具，同时保留其他可用工具
pi --exclude-tools ask_question
```

### 环境变量 Environment Variables

| 变量 | 描述 |
| ------ | ------ |
| `PI_CODING_AGENT_DIR` | 覆盖配置目录；默认为 `~/.pi/agent` |
| `PI_CODING_AGENT_SESSION_DIR` | 覆盖会话存储目录；被 `--session-dir` 覆盖 |
| `PI_PACKAGE_DIR` | 覆盖包目录，适用于 Nix/Guix 存储路径 |
| `PI_OFFLINE` | 禁用启动时的网络操作，包括更新检查、包更新检查以及安装/更新遥测 |
| `PI_SKIP_VERSION_CHECK` | 跳过启动时的 Pi 版本更新检查。阻止向 `pi.dev` 请求最新版本 |
| `PI_TELEMETRY` | 覆盖安装/更新遥测和提供商归属标头：`1`/`true`/`yes` 或 `0`/`false`/`no`。这不会禁用更新检查 |
| `PI_CACHE_RETENTION` | 设置为 `long` 可在支持的情况下使用扩展提示缓存 |
| `VISUAL`, `EDITOR` | 当 `externalEditor` 未设置时，作为 Ctrl+G 的回退外部编辑器；Windows 上默认为记事本，其他平台默认为 `nano` |

## 设计原则 Design Principles

Pi 保持核心小巧，将工作流特定行为推送到扩展、技能、提示模板和包中。

它有意不包含内置的 MCP、子 agent、权限弹窗、计划模式、待办事项或后台 bash。你可以作为扩展或包构建或安装这些工作流，或使用外部工具（如容器和 tmux）。

完整的设计理念请阅读[博客文章](https://mariozechner.at/posts/2025-11-30-pi-coding-agent/)。
