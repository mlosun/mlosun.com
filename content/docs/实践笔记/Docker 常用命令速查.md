---
created: 2024-12-01
title: Docker 常用命令速查
tags: [Docker]
description: 日常使用频率最高的 Docker 命令和配置文件说明。
---

> 列出日常使用频率最高的命令。更多可参考 [Docker 命令大全 \| 菜鸟教程](https://www.runoob.com/docker/docker-command-manual.html)

## 容器管理

| 命令 | 说明 |
| --- | --- |
| `docker ps` | 列出运行中的容器 |
| `docker ps -a` | 列出所有容器（含已停止的） |
| `docker start <容器>` | 启动已停止的容器 |
| `docker stop <容器>` | 停止运行中的容器 |
| `docker restart <容器>` | 重启容器 |
| `docker rm <容器>` | 删除已停止的容器 |
| `docker rm -f <容器>` | 强制删除运行中的容器 |
| `docker rename <容器> <新名称>` | 重命名容器 |

## 镜像管理

| 命令 | 说明 |
| --- | --- |
| `docker images` | 列出本地镜像 |
| `docker pull <镜像名>` | 从仓库拉取镜像，如 `docker pull nginx` |
| `docker rmi <镜像名>` | 删除本地镜像 |
| `docker build -t <标签> .` | 构建镜像，`-t` 指定名称和标签 |
| `docker tag <镜像> <新标签>` | 给镜像打标签 |
| `docker push <镜像名>` | 推送镜像到远程仓库 |

## 日志与调试

| 命令 | 说明 |
| --- | --- |
| `docker logs <容器>` | 查看容器日志 |
| `docker logs -f --tail 100 <容器>` | 实时查看最近 100 条日志 |
| `docker exec -it <容器> bash` | 进入容器内部（交互式 Shell） |
| `docker inspect <容器>` | 查看容器的详细信息（JSON） |
| `docker stats` | 实时显示容器资源占用（CPU/内存） |
| `docker top <容器>` | 查看容器内运行的进程 |

## Docker Compose

| 命令 | 说明 |
| --- | --- |
| `docker compose up -d` | 后台启动所有服务 |
| `docker compose down` | 停止并移除所有容器和网络 |
| `docker compose ps` | 列出项目中的容器状态 |
| `docker compose logs -f` | 实时查看所有服务的日志 |
| `docker compose restart` | 重启所有服务 |
| `docker compose pull` | 拉取所有服务的最新镜像 |

## 系统维护

| 命令 | 说明 |
| --- | --- |
| `docker system df` | 查看磁盘使用情况 |
| `docker system prune` | 清理未使用的容器、镜像、网络 |
| `docker system prune -a` | 清理所有未使用的资源（含未使用的镜像） |
| `docker info` | 查看 Docker 系统信息 |
