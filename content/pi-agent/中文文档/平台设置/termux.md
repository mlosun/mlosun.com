---
title: "Termux"
description: "Pi-Coding-Agent 中文文档 — Android Termux 设置"
weight: 240
created: 2025-07-16
---

Pi 通过 [Termux](https://termux.dev/) 在 Android 上运行，Termux 是一款适用于 Android 的终端模拟器和 Linux 环境。

## 先决条件 Prerequisites

1. 从 GitHub 或 F-Droid 安装 [Termux](https://github.com/termux/termux-app#installation)（不要从 Google Play 安装，该版本已废弃）
2. 从 GitHub 或 F-Droid 安装 [Termux:API](https://github.com/termux/termux-api#installation)，用于剪贴板和其他设备集成功能

## 安装 Installation

```bash
# 更新包
pkg update && pkg upgrade

# 安装依赖
pkg install nodejs termux-api git

# 安装 pi
npm install -g --ignore-scripts @earendil-works/pi-coding-agent

# 创建配置目录
mkdir -p ~/.pi/agent

# 运行 pi
pi
```

## 剪贴板支持 Clipboard Support

在 Termux 中运行时，剪贴板操作使用 `termux-clipboard-set` 和 `termux-clipboard-get`。需要安装 Termux:API 应用才能使用这些功能。

Termux 不支持图片剪贴板（`ctrl+v` 粘贴图片功能不可用）。

## Termux 的 AGENTS.md 示例 Example AGENTS.md for Termux

创建 `~/.pi/agent/AGENTS.md` 以帮助代理理解 Termux 环境：

````markdown
# Agent Environment: Termux on Android

## Location
- **OS**: Android (Termux terminal emulator)
- **Home**: `/data/data/com.termux/files/home`
- **Prefix**: `/data/data/com.termux/files/usr`
- **Shared storage**: `/storage/emulated/0` (Downloads, Documents, etc.)

## Opening URLs
```bash
termux-open-url "https://example.com"
```

## Opening Files
```bash
termux-open file.pdf          # Opens with default app
termux-open --chooser image.jpg      # Choose app
```

## Clipboard
```bash
termux-clipboard-set "text"   # Copy
termux-clipboard-get          # Paste
```

## Notifications
```bash
termux-notification -t "Title" -c "Content"
```

## Device Info
```bash
termux-battery-status         # Battery info
termux-wifi-connectioninfo    # WiFi info
termux-telephony-deviceinfo   # Device info
```

## Sharing
```bash
termux-share -a send file.txt # Share file
```

## Other Useful Commands
```bash
termux-toast "message"        # Quick toast popup
termux-vibrate                # Vibrate device
termux-tts-speak "hello"      # Text to speech
termux-camera-photo out.jpg   # Take photo
```

## Notes
- Termux:API app must be installed for `termux-*` commands
- Use `pkg install termux-api` for the command-line tools
- Storage permission needed for `/storage/emulated/0` access
````

## 限制 Limitations

- **无图片剪贴板**：Termux 剪贴板 API 仅支持文本
- **无原生二进制文件**：某些可选的本地依赖（如剪贴板模块）在 Android ARM64 上不可用，安装时会被跳过
- **存储访问**：要访问 `/storage/emulated/0`（Downloads 等）中的文件，运行 `termux-setup-storage` 一次以授予权限

## 故障排除 Troubleshooting

### 剪贴板无法工作 Clipboard not working

确保已安装以下两个应用：

1. Termux（从 GitHub 或 F-Droid 安装）
2. Termux:API（从 GitHub 或 F-Droid 安装）

然后安装命令行工具：

```bash
pkg install termux-api
```

### 共享存储权限被拒绝 Permission denied for shared storage

运行以下命令一次以授予存储权限：

```bash
termux-setup-storage
```

### Node.js 安装问题 Node.js installation issues

如果 npm 失败，请尝试清除缓存：

```bash
npm cache clean --force
```
