---
title: "superpowers-zh — AI 编程超能力 · 中文增强版"
created: 2026-07-17
description: "20 个 AI 编程方法论 skills，完整汉化 + 6 个中国特色。TDD、调试、代码审查、头脑风暴一站式配齐，装上就让 Pi 从聊天工具升级成专业搭档。"
tags: ["pi", "package", "skills", "tdd", "code-review", "debugging", "brainstorming"]
---

## 简介

`superpowers-zh` 是 Pi 的「方法论操作系统」。它把业界最火的 AI 编程 skills 框架 [superpowers](https://github.com/obra/superpowers)（250k+ stars）完整汉化并增强，**20 个 skills 一键安装**，让 Pi 从"你问一句它答一句"变成真正会干活的专业搭档。

没装之前，你跟 Pi 说"给用户模块加个批量导出功能"，它可能直接开始写代码——不管格式对不对、有没有分页、大数据量会不会 OOM。装了之后，Pi 会先反问需求、给方案选项、确认后再动手，每一步都有章法。

最方便的是——**一条命令装完即用**，Pi 的包机制自动挂载所有 skills，不用手动配置任何东西。

## Skills 一览

### 翻译的 Skills（14 个）

| Skill | 用途 |
|-------|------|
| **头脑风暴** (brainstorming) | 需求分析 → 设计规格，不写代码先想清楚 |
| **编写计划** (writing-plans) | 把规格拆成可执行的实施步骤 |
| **执行计划** (executing-plans) | 按计划逐步实施，每步验证 |
| **测试驱动开发** (test-driven-development) | 严格 TDD：先写测试，再写代码 |
| **系统化调试** (systematic-debugging) | 四阶段调试法：定位 → 分析 → 假设 → 修复 |
| **请求代码审查** (requesting-code-review) | 派遣审查 agent 检查代码质量（需 `pi-subagents`） |
| **接收代码审查** (receiving-code-review) | 技术严谨地处理审查反馈，拒绝敷衍 |
| **完成前验证** (verification-before-completion) | 证据先行——声称完成前必须跑验证 |
| **派遣并行 Agent** (dispatching-parallel-agents) | 多任务并发执行（需 `pi-subagents`） |
| **子 Agent 驱动开发** (subagent-driven-development) | 每个任务一个 agent，两轮审查（需 `pi-subagents`） |
| **Git Worktree 使用** (using-git-worktrees) | 隔离式特性开发 |
| **完成开发分支** (finishing-a-development-branch) | 合并 / PR / 保留 / 丢弃四选一 |
| **编写 Skills** (writing-skills) | 创建新 skill 的方法论 |
| **使用 Superpowers** (using-superpowers) | 元技能：如何调用和优先使用 skills |

### 中国特色 Skills（6 个）

| Skill | 用途 | 调用方式 |
|-------|------|----------|
| **中文代码审查** (chinese-code-review) | 符合国内团队文化的代码审查规范 | `/chinese-code-review`（手动） |
| **中文 Git 工作流** (chinese-git-workflow) | Gitee / Coding / 极狐 GitLab / CNB 配置参考 | `/chinese-git-workflow`（手动） |
| **中文技术文档** (chinese-documentation) | 中文排版规范、中英混排、告别机翻味 | `/chinese-documentation`（手动） |
| **中文提交规范** (chinese-commit-conventions) | 适配国内团队的 commit message 规范 | `/chinese-commit-conventions`（手动） |
| **MCP 服务器构建** (mcp-builder) | 构建生产级 MCP 工具，扩展 AI 能力边界 | 自动触发 |
| **工作流执行器** (workflow-runner) | 在 AI 工具内运行多角色 YAML 工作流 | 自动触发 |

> 前 4 个 chinese-* 为手动调用 skill，不会自动触发，需在对话中显式输入 `/chinese-xxx` 才会加载。这样设计是为了避免污染上游 skill 的自动调度。

## 安装配置

```bash
pi install npm:superpowers-zh
```

零配置开箱即用。安装后 Pi 自动挂载所有 skills，每次会话启动时注入引导，Agent 会在合适时机自行调用对应 skill。

## 使用场景

- **接需求不跑偏** — Agent 不急着写代码，先跟你对齐边界："导出是 CSV 还是 Excel？数据量多大？需要权限控制吗？"，给 2-3 个方案确认后再动手
- **从零实现功能** — 先自动生成测试用例，你确认测试合理后，再写实现代码，最后跑测试验证，一步不跳
- **碰到诡异 Bug** — 不再"测一下这个试试"，而是系统化地复现、定位根因、提出假设、验证修复，四步闭环
- **收到审查意见后** — 面对含糊或矛盾的反馈不盲改，先追问澄清、验证技术假设，再动手改代码
- **合并前最后一步** — 声称"做完了"之前，Pi 先跑一遍验证命令，确认输出通过才让你提交
- **新功能隔离开发** — 自动创建 git worktree，在独立目录里开发，不影响当前分支，完事自动收尾
- **想写个 MCP 工具** — 从规划设计到测试发布，全程有人带路，不用自己摸索最佳实践
- **团队协作规范** — 一键配好 commitlint + husky + commitizen 中文模板，提交信息不再乱七八糟

## 与英文上游的区别

| 维度 | superpowers（英文上游） | superpowers-zh（中文增强版） |
|------|------------------------|------------------------------|
| Skills 总数 | 14 | **20**（14 翻译 + 6 国产原创） |
| 支持工具 | 6 款 | **20 款**（含 Pi） |
| 语言 | 英文 | 中文（技术术语保留英文） |
| 安装方式 | 按工具分别装 | 一条 `pi install` 搞定 |
| 中国 Git 平台 | 仅 GitHub | GitHub + Gitee + Coding + 极狐 + CNB |
| 代码审查风格 | 西方直接风格 | 适配国内团队沟通文化 |
| MCP / 工作流 | 无 | 独立 `mcp-builder` + `workflow-runner` |

## 参考链接

- [官方仓库](https://github.com/jnMetaCode/superpowers-zh) — 源码、Issue、更新日志
- [pi.dev 包页面](https://pi.dev/packages/superpowers-zh) — 官方文档
- [npm 包页面](https://www.npmjs.com/package/superpowers-zh) — 版本历史
- [英文上游](https://github.com/obra/superpowers) — 原始 superpowers（MIT）
- [Pi 安装指南](https://cdn.jsdelivr.net/npm/superpowers-zh@1.7.0/docs/README.pi.md) — Pi 专属安装文档
