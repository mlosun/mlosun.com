---
created: 2024-10-27
title: Git 常用命令速查
weight: 5
description: 日常使用频率最高的 Git 命令速查表，涵盖基本操作、分支管理、查看历史、撤销与暂存、远程协作等场景。
tags: [Git]
---

> 本文基于 Git 2.46.0，日常使用中如有更多需求可查看 `git help` 或官方文档。

## 基本操作

| 命令 | 说明 |
| --- | --- |
| `git init` | 在当前目录初始化一个新的 Git 仓库 |
| `git clone <地址>` | 克隆远程仓库到本地 |
| `git add .` | 暂存所有修改过的文件 |
| `git add <文件>` | 暂存指定文件 |
| `git commit -m "说明"` | 提交暂存区的内容 |
| `git commit -am "说明"` | 暂存所有已跟踪文件并提交（跳过 `git add`） |
| `git status` | 查看当前工作区状态 |

## 分支管理

| 命令 | 说明 |
| --- | --- |
| `git branch` | 列出本地分支（当前分支前有 `*`） |
| `git branch -a` | 列出所有分支（含远程分支） |
| `git branch <名称>` | 创建新分支 |
| `git branch -d <名称>` | 删除已合并的分支 |
| `git branch -D <名称>` | 强制删除分支（即使未合并） |
| `git switch <名称>` | 切换到已有分支 |
| `git switch -c <名称>` | 创建并切换到新分支 |
| `git merge <分支>` | 将指定分支合并到当前分支 |
| `git rebase <分支>` | 将当前分支变基到指定分支 |

## 查看历史

| 命令 | 说明 |
| --- | --- |
| `git log` | 查看提交历史 |
| `git log --oneline` | 单行显示提交历史 |
| `git log --oneline --graph` | 以图形方式显示分支历史 |
| `git log -p` | 显示每次提交的 diff |
| `git diff` | 比较工作区和暂存区的差异 |
| `git diff --staged` | 比较暂存区和上次提交的差异 |
| `git show` | 查看最新提交的详细信息 |
| `git show <commit-id>` | 查看指定提交的详细信息 |

## 撤销与暂存

| 命令 | 说明 |
| --- | --- |
| `git restore <文件>` | 撤销工作区的修改（未暂存的文件） |
| `git restore --staged <文件>` | 取消暂存（文件回到工作区） |
| `git reset --soft HEAD~1` | 撤销上次提交，保留修改 |
| `git reset --hard HEAD~1` | 撤销上次提交，丢弃修改（慎用） |
| `git stash` | 暂存当前工作区的修改 |
| `git stash pop` | 恢复最近一次暂存并删除它 |
| `git stash list` | 查看暂存列表 |

## 远程协作

| 命令 | 说明 |
| --- | --- |
| `git remote -v` | 查看远程仓库地址 |
| `git remote add origin <地址>` | 添加远程仓库 |
| `git push origin <分支>` | 推送代码到远程 |
| `git push -u origin <分支>` | 首次推送并建立 upstream 关联 |
| `git pull` | 拉取远程代码并合并 |
| `git fetch` | 拉取远程代码但不合并 |
| `git clone <地址>` | 克隆远程仓库 |

## 配置

| 命令 | 说明 |
| --- | --- |
| `git config --list` | 查看当前所有配置 |
| `git config --global user.name "名称"` | 设置全局用户名 |
| `git config --global user.email "邮箱"` | 设置全局邮箱 |
| `git config user.name "名称"` | 设置当前仓库的用户名 |
| `git config user.email "邮箱"` | 设置当前仓库的邮箱 |
| `git config --global http.proxy <地址>` | 设置 HTTP 代理 |
| `git config --global --unset http.proxy` | 取消 HTTP 代理 |

## 其他

| 命令 | 说明 |
| --- | --- |
| `git tag` | 列出所有标签 |
| `git tag <名称>` | 创建轻量标签 |
| `git mv <旧文件> <新文件>` | 重命名或移动文件 |
| `git rm <文件>` | 从仓库中删除文件 |
| `git clean -df` | 删除未跟踪的文件和目录 |
| `git help` | 查看 Git 帮助信息 |
| `git -v` | 查看 Git 版本 |
