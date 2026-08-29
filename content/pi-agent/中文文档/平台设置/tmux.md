---
title: "tmux"
description: "Pi-Coding-Agent 中文文档 — tmux 设置"
weight: 250
created: 2025-07-16
---

Pi 运行在 tmux 内，但 tmux 默认会剥离某些按键的修饰键信息。若不进行配置，`Shift+Enter` 和 `Ctrl+Enter` 通常无法与普通 `Enter` 区分。

## 推荐配置 (Recommended Configuration)

添加到 `~/.tmux.conf`：

```tmux
set -g extended-keys on
set -g extended-keys-format csi-u
```

然后完全重启 tmux：

```bash
tmux kill-server
tmux
```

当 Kitty 键盘协议不可用时，Pi 会自动请求扩展键报告。使用 `extended-keys-format csi-u` 时，tmux 以 CSI-u 格式转发修饰键，这是最可靠的配置。`extended-keys-format` 选项需要 tmux 3.5 或更高版本。

## 为何推荐 `csi-u` (Why `csi-u` Is Recommended)

如果仅使用：

```tmux
set -g extended-keys on
```

tmux 默认采用 `extended-keys-format xterm`。当应用程序请求扩展键报告时，修饰键将以 xterm 的 `modifyOtherKeys` 格式转发，例如：

- `Ctrl+C` → `\x1b[27;5;99~`
- `Ctrl+D` → `\x1b[27;5;100~`
- `Ctrl+Enter` → `\x1b[27;5;13~`

使用 `extended-keys-format csi-u` 后，相同的按键将以下列格式转发：

- `Ctrl+C` → `\x1b[99;5u`
- `Ctrl+D` → `\x1b[100;5u`
- `Ctrl+Enter` → `\x1b[13;5u`

Pi 同时支持两种格式，但 `csi-u` 是推荐的 tmux 配置。

## 修复效果 (What This Fixes)

未启用 tmux 扩展键时，修饰后的 Enter 键会退化为传统序列：

| 按键 | 无扩展键 | 启用 `csi-u` |
| ------- | ----------- | -------------- |
| Enter | `\r` | `\r` |
| Shift+Enter | `\r` | `\x1b[13;2u` |
| Ctrl+Enter | `\r` | `\x1b[13;5u` |
| Alt/Option+Enter | `\x1b\r` | `\x1b[13;3u` |

这会影响默认按键绑定（`Enter` 提交，`Shift+Enter` 换行）以及任何使用修饰 Enter 的自定义按键绑定。

## 环境要求 (Requirements)

- tmux 3.5 或更高版本（运行 `tmux -V` 检查版本）以支持 `extended-keys-format csi-u`
- 支持扩展键的终端模拟器（Ghostty、Kitty、iTerm2、WezTerm、Windows Terminal）

如果使用 tmux 3.2 至 3.4 版本，请省略 `extended-keys-format csi-u`；Pi 仍支持 tmux 默认的 xterm `modifyOtherKeys` 格式。
