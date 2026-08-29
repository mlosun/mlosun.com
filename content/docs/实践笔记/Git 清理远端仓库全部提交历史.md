---
created: 2024-09-11
title: Git 清理远端仓库全部提交历史
tags: [Git, GitHub]
description: 删除 Git 仓库全部提交历史但保留仓库本身，适用于清理隐私信息或给仓库瘦身。
---
> [!warning] 注意
> 此操作不可逆，会永久删除仓库的所有提交历史，请提前做好备份。

## 背景

当提交记录中包含隐私信息（如密钥、密码），或仓库因上传大量文件导致体积过大、拉取频繁出错时，可以通过清除全部提交历史来"瘦身"仓库，同时保留仓库本身（Star、Issues 等不受影响）。

以下两种方法均以 `main` 分支为例，操作前请先将仓库克隆到本地。

## 方法一：创建空白分支替换

通过 `--orphan` 创建一个没有历史的新分支，替换掉原来的 `main`：

```shell
git checkout --orphan latest_branch  # 创建一个没有历史的新分支
git add -A  # 暂存所有文件
git commit -m "Initial commit"  # 创建初始提交
git branch -D main  # 删除本地的 main 分支
git branch -m main  # 将当前分支重命名为 main
git push -f origin main  # 强制推送到远程仓库，替换远程 main 分支
```

## 方法二：删除 .git 重新初始化

直接删除本地的 Git 历史，重新初始化一个全新的仓库：

```shell
rm -rf .git  # 删除本地 Git 仓库目录
git init  # 重新初始化一个新的 Git 仓库
git add -A  # 暂存所有文件
git commit -m "Initial commit"  # 创建初始提交
git push -f https://github.com/username/repo.git main  # 强制推送到远程仓库（需替换为实际仓库地址）
```

> \[!tip] 两种方法的区别
>
> * 方法一保留了本地 Git 配置（如 remote、branch tracking），操作更简单
> * 方法二完全重置仓库，但需要手动填写远程仓库地址，且会丢失所有本地 Git 配置

## 参考资料

* [删除 GitHub 上的 commit 记录 - 博客园](https://www.cnblogs.com/oldweipro/p/16634523.html)
