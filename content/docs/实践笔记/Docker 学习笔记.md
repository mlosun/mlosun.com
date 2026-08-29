---
created: 2026-02-11
title: Docker 学习笔记
tags: [Docker]
---

> [!NOTE] 说明
> 本文是根据 [40分钟的Docker实战攻略，一期视频精通Docker](https://www.bilibili.com/video/BV1THKyzBER6/) 视频学习记录的笔记。
> [参考评论1](https://www.bilibili.com/video/BV1THKyzBER6?comment_on=1&comment_root_id=270940020720&share_tag=s_i#reply270940020720) [参考评论2](https://www.bilibili.com/video/BV1THKyzBER6?comment_on=1&comment_root_id=283732220368&comment_secondary_id=291493627440&share_tag=s_i#reply291493627440)

## Docker 核心概念

### 容器化技术

- Docker 是目前最成熟高效的软件部署技术
- Docker 就是用容器化技术给应用程序封装独立的运行环境
- 每个运行环境就是一个==容器==
- 运行容器的计算机被称为==宿主机==

### 容器与虚拟机的需求

- 容器：多个容器共用同一个系统内核
- 虚拟机：每个虚拟机都包含操作系统的完整内核
- 优势：Docker 容器比虚拟机更轻、更小，启动速度更快

### 镜像与容器的关系

- 镜像：镜像是容器的模板，可类比为软件安装包
- 容器：由“软件安装包”安装出来的软件
- 类比：
  - 类比于用模具做糕点，镜像是模具，容器是做出来的糕点
  - 可以使用一个模具（镜像）做出很多个糕点（容器）
  - 也可以将模具（镜像）分享给其他人使用

### Docker 仓库

- Docker 仓库是用来存放和分享镜像的地方
- 用户可以将镜像上传到仓库后，供其他人下载
- [Docker Hub](https://hub.docker.com) 是 Docker 的官方仓库，可以搜索大量镜像

## Docker 安装方式

> 暂略~
> 我日常使用 ==1Panel==、==cnb.cool==、==群晖 Nas== 这些方式管理 Docker 容器
> 他们都已经配置好了 Docker 环境
> 暂时不需要去了解 Docker 的安装

## Docker 镜像管理

### 镜像地址

一个完整的镜像地址包含以下四部分：

```
docker.io/library/nginx:latest
<仓库地址>/<命名空间>/<镜像名称>:<版本标签>
```

- `docker.io` 是 Docker Hub 官方仓库的地址，可以省略
- `library` 是 Docker Hub 官方仓库的命名空间，可以省略
- `nginx` 是镜像名称
- `latest` 是版本标签，`latest`表示最新版本，可以省略
- 示例：
  - 从官方仓库下载最新版 nginx  镜像: `docker pull nginx`
  - 从官方仓库的 n8n 命名空间下载镜像: `docker pull n8nio/n8n`
  - 从 n8n 私有仓库下载镜像: `docker pull docker.n8n.io/n8nio/n8n`

> 官方仓库的镜像往往无法直接下载，需要寻找相应的镜像站或使用代理。

//TODO
