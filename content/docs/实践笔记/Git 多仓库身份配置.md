---
created: 2024-08-12
title: Git 多仓库身份配置
tags: [Git]
description: 在个人项目和公司项目中使用不同的 Git 用户名和邮箱提交，避免身份混淆。
---

工作电脑上同时有个人项目和公司项目时，如果不做配置，所有提交都会使用同一个用户名和邮箱。这篇文章教你如何让 Git 根据仓库自动切换身份。

## 配置优先级

Git 采用三层配置覆盖，优先级**从低到高**：

```
系统级 → 全局级 → 仓库级
```

仓库级配置会覆盖全局级，所以只需要在个别仓库设置独立身份即可。

## 操作步骤

### 1. 设置全局默认身份

适用于大多数仓库（比如你的个人项目）：

```bash
git config --global user.name "your-name"
git config --global user.email "your@email.com"
```

### 2. 设置单个仓库的独立身份

终端切换到需要单独设置的仓库目录，不加 `--global`：

```bash
cd /path/to/company-project
git config user.name "work-name"
git config user.email "work@company.com"
```

这条命令会写入当前仓库的 `.git/config`，只影响这个仓库。

### 3. 验证当前仓库身份

```bash
git config user.name
git config user.email
```

### 手动编辑配置

也可以直接打开仓库下的 `.git/config` 文件，找到或添加：

```ini
[user]
    name = work-name
    email = work@company.com
```

## 常见场景

| 场景 | 全局身份 | 仓库级身份 |
| :-- | :-- | :-- |
| 个人 GitHub 项目 + 公司 GitLab 项目 | 个人邮箱 | 公司邮箱 |
| 开源项目 + 内部项目 | 个人昵称 | 公司实名 |
| 多个账号的 GitHub | 主账号 | 副账号 |
