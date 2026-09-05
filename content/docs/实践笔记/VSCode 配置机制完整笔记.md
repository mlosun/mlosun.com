---
created: 2026-03-09
title: VSCode 配置机制完整笔记
weight: 12
tags: [VSCode, 配置]
---
## 一、.vscode 目录概述

`.vscode` 是 VS Code 的**项目级配置目录**，定义"当 VS Code 打开这个项目时，该如何表现"。

### 常见文件结构

```text
.vscode/
├── settings.json      # 工作区设置（覆盖用户全局设置）
├── extensions.json    # 推荐安装的扩展列表
├── launch.json        # 调试配置
├── tasks.json         # 自定义任务
└── snippets/          # 代码片段目录
```

***

## 二、配置层级与覆盖机制

### 三层架构（优先级由低到高）

```text
VS Code 默认设置
    ↓
用户级设置（~/settings.json）
    ↓
工作区设置（.vscode/settings.json）← 项目级，优先级最高
```

### 关键区别

| 文件 | 机制 | 行为 |
| ----------------- | -------- | ------------------------ |
| `settings.json` | **增量覆盖** | 只写3项 → 继承用户级其余7项 |
| `extensions.json` | **全量清单** | 只写a,b,c → 只推荐a,b,c，不继承其他 |
| `launch.json` | **全量配置** | 只显示配置的调试方案 |
| `tasks.json` | **全量配置** | 只显示定义的任务 |
| `snippets/*.json` | **全量配置** | 只提供定义的代码片段 |

### 示例对比

#### settings.json（增量）

```json
// 用户级：a=1, b=2, c=3, d=4, e=5
// 项目级写：{"b": 20}
// 结果：a=1, b=20, c=3, d=4, e=5（继承+覆盖）
```

#### extensions.json（全量）

```json
// 用户级：安装了 a,b,c,d
// 项目级写：{"recommendations": ["a","b"]}
// 结果：只推荐 a,b（不会推荐 c,d）
```

***

## 三、配置存储位置

### 用户级配置

| 类型 | 路径（macOS/Linux） | 路径（Windows） |
| ---- | -------------------------------------- | ----------------------------------- |
| 设置文件 | `~/.config/Code/User/settings.json` | `%APPDATA%\Code\User\settings.json` |
| 扩展目录 | `~/.vscode/extensions/` | `%USERPROFILE%\.vscode\extensions\` |
| 快捷键 | `~/.config/Code/User/keybindings.json` | 同左 |
| 代码片段 | `~/.config/Code/User/snippets/` | 同左 |

### 项目级配置

* 位置：`.vscode/` 目录下
* 作用范围：仅当前项目
* 版本控制：**应提交到 Git**，确保团队一致

***

## 四、扩展管理

### 用户级 vs 项目级

| 级别 | 存储位置 | 特点 |
| ----- | ------------------------- | ------------- |
| 用户级扩展 | `~/.vscode/extensions/` | 所有项目共享，物理安装在此 |
| 项目级推荐 | `.vscode/extensions.json` | 仅提示安装，不自动部署 |
| 真正隔离 | Dev Container | 容器内独立安装扩展 |

### 扩展推荐配置

```json
{
  "recommendations": ["dbaeumer.vscode-eslint", "esbenp.prettier-vscode"],
  "unwantedRecommendations": ["some.extension.to.disable"]
}
```

### 多项目扩展隔离方案

**场景**：项目A用a,b,c，项目B用d,e,f，其他项目用a,b,c,d

| 方案 | 实现方式 | 适用场景 |
| ---- | ------------------------ | -------- |
| 简单推荐 | `extensions.json` 分别配置 | 只是提示，不强制 |
| 禁用配置 | `settings.json` 中禁用对方扩展 | 扩展有冲突时 |
| 彻底隔离 | `--user-data-dir` 启动独立实例 | 需要完全隔离 |

***

## 五、跨平台路径处理

### 核心原则

> **能用相对不用绝对，能用变量不用字符串**

### 内置变量

| 变量 | 含义 | 跨平台效果 |
| ---------------------------- | ------ | -------------------------------------------- |
| `${workspaceFolder}` | 项目根目录 | 自动适配 Windows/macOS/Linux 路径格式 |
| `${userHome}` | 用户主目录 | Windows: `C:\Users\123`, macOS: `/Users/123` |
| `${workspaceFolderBasename}` | 项目文件夹名 | 如 `project` |
| `${env:NAME}` | 环境变量 | 如 `${env:HOME}` |

### 正确 vs 错误示例

```json
// ❌ 错误：硬编码绝对路径
"python.defaultInterpreterPath": "C:/Users/123/project/venv/bin/python"

// ✅ 正确：使用变量
"python.defaultInterpreterPath": "${workspaceFolder}/venv/bin/python"
"python.analysis.extraPaths": ["${userHome}/.local/lib/python"]
```

### 特殊情况处理

如果路径**完全无关**（如 Windows `D:\project1\123` vs macOS `Documents/project1/123`）：

| 方案 | 做法 |
| -- | ------------------------------------- |
| 推荐 | 把系统特定路径放**用户级 settings.json**，项目级保持通用 |
| 备选 | 使用**环境变量** `${env:PROJECT1_PATH}` |
| 避免 | 硬编码不同系统的绝对路径到项目配置 |

***

## 六、VS Code 设置同步

### 同步内容

* ✅ 用户级 `settings.json`
* ✅ 扩展列表（自动重新安装）
* ✅ 键盘快捷键
* ✅ 用户级代码片段
* ✅ UI 状态

### 不同步内容

* ❌ 项目级 `.vscode/` 目录（需 Git 管理）
* ❌ 扩展的本地数据/缓存

### 分工明确

| 机制 | 用途 |
| ------------------- | -------------- |
| **设置同步** | 个人习惯跟着账号走（跨设备） |
| **`.vscode` + Git** | 团队规范跟着代码走（跨成员） |

***

## 七、最佳实践总结

### 1. 配置原则

* **项目级**（`.vscode/`）：放团队共享、通用、跨平台的配置
* **用户级**（`~/settings.json`）：放个人偏好、系统特定的配置

### 2. 路径原则

* 优先使用 `${workspaceFolder}`（项目内）
* 其次使用 `${userHome}`（用户目录）
* 避免硬编码盘符（如 `D:\`、`/Volumes/`）

### 3. 扩展原则

* 项目必需的扩展 → `extensions.json` 推荐
* 个人辅助扩展 → 用户级安装，不同步到项目

### 4. 调试/任务原则

* 通用任务 → `.vscode/tasks.json`（全量定义）
* 个人调试习惯 → 用户级 `launch.json`

***

## 八、快速参考命令

| 操作 | 命令面板（Ctrl+Shift+P） |
| ------------- | --------------------------------------------- |
| 打开用户级设置（UI） | `Preferences: Open User Settings` |
| 打开用户级设置（JSON） | `Preferences: Open User Settings (JSON)` |
| 打开工作区设置（JSON） | `Preferences: Open Workspace Settings (JSON)` |
| 打开扩展目录 | `Extensions: Open Extensions Folder` |
| 打开键盘快捷键 | `Preferences: Open Keyboard Shortcuts (JSON)` |
