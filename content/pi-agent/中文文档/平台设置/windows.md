---
title: "Windows"
description: "Pi-Coding-Agent 中文文档 — Windows 安装"
weight: 230
created: 2025-07-16
---

Pi 在 Windows 上需要 bash shell。按以下顺序检查位置：

1. `~/.pi/agent/settings.json` 中的自定义路径
2. Git Bash（`C:\Program Files\Git\bin\bash.exe`）
3. PATH 上的 `bash.exe`（Cygwin、MSYS2、WSL）

对于大多数用户，[Git for Windows](https://git-scm.com/download/win) 已经足够。

## 自定义 Shell 路径 Custom Shell Path

```json
{
  "shellPath": "C:\\cygwin64\\bin\\bash.exe"
}
```
