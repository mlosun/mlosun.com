---
title: "Shell 别名"
description: "Pi-Coding-Agent 中文文档 — Shell 别名"
weight: 270
created: 2025-07-16
---

Pi 以非交互模式运行 bash（`bash -c`），默认情况下不会展开别名。

要启用你的 shell 别名，请将其添加到 `~/.pi/agent/settings.json`：

```json
{
  "shellCommandPrefix": "shopt -s expand_aliases\neval \"$(grep '^alias ' ~/.zshrc)\""
}
```

根据你的 shell 配置调整路径（`~/.zshrc`、`~/.bashrc` 等）。
