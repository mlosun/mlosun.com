---
created: 2024-10-07
title: Docker 远程管理 Portainer
weight: 4
tags: [Docker]
description: 通过 Portainer 的 Web 界面远程管理多台主机的 Docker 容器。
---

> [!warning] 2024-10-08 update 安全提醒
> 直接暴露 Docker API 端口（2375）到公网没有任何加密和认证。配置完成后务必限制防火墙来源 IP，或配置 TLS 加密。否则可能被加密劫持蠕虫盯上。
>
> 配置好远程管理后不久，我收到了云服务器恶意文件通知，并在远程主机中发现了一个莫名的容器 `boorish_agelast`，运行着 `ubuntu:18.04`。经确认是[针对 Docker 守护进程的加密劫持蠕虫 Cetus](https://unit42.paloaltonetworks.com/cetus-cryptojacking-worm/)。如果你的 Docker 环境不需要频繁集中管理，建议先配置好 TLS 加密再开放远程端口：
>
> - [如何开启 Docker Remote API 的 TLS 认证，并在 Portainer 上进行配置](https://www.xukecheng.tech/how-to-enable-tls-authentication-for-docker-remote-api)
> - [Docker 启用 TLS 进行安全配置 - JadePeng](https://www.cnblogs.com/xiaoqi/p/docker-tls.html)

本文内容基于 [6053537/portainer-ce - Docker Image | Docker Hub](https://hub.docker.com/r/6053537/portainer-ce) 的汉化版。

## 远程主机设置

1. 确保远程主机已安装 Docker

2. 进入主机终端，输入命令：

```bash
nano /usr/lib/systemd/system/docker.service
```

3. 然后做以下修改：

```bash
# 将这一行
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

# 改为这样
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H fd:// --containerd=/run/containerd/containerd.sock
```

4. 最后再重启 Docker 即可

```bash
systemctl daemon-reload
systemctl restart docker
```

5. 记得在服务器的防火墙放开 2375 端口

## Portainer 设置

1. 环境 - 添加环境 - 独立的 Docker
2. 选择 API 连接模式
3. Docker API URL 处填写被远程连接的主机 IP+ 端口，如 `6.6.6.6:2375`
4. 点击链接，完成。

## 参考资料

- [部署 Portainer 来管理本地或远程的 Docker - Moeyukina's Blog](https://blog.moeyukina.top/index.php/2022/11/02/deploying-portainer-managing-docker/)
