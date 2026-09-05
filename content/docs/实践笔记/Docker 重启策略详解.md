---
created: 2024-11-29
title: Docker 重启策略详解
weight: 3
tags: [Docker]
---

Docker 容器的重启策略有四种：`no`、`always`、`on-failure`、`unless-stopped`。

| 情况 | no | always | on-failure | unless-stopped |
| --- | --- | --- | --- | --- |
| 容器正常退出 (0 状态) | 不重启 | 重启 | 不重启 | 重启 |
| 容器非正常退出 (非 0 状态) | 不重启 | 重启 | 重启 | 重启 |
| 手动停止 (`docker stop`) | 不重启 | 不重启 | 不重启 | 不重启 |
| Docker 服务重启 (如系统重启) | 不重启 | 重启 | 不重启 | 不重启 |

下面通过一个 demo 来演示它们的区别。

## 准备测试环境

创建一个名为 `test.sh` 的脚本，5 秒后自动退出：

```bash
#!/bin/bash
sleep 5
exit 1
```

创建 Dockerfile 将其打包进镜像：

```dockerfile
FROM ubuntu
ADD test.sh /
CMD /bin/bash /test.sh
```

构建镜像：

```bash
docker build -t test-restart .
```

## no

```bash
docker run -d --name=test-restart-no test-restart
```

容器 5 秒后退出，docker daemon 不做任何处理。

**结论：容器退出后不重启。**

## always

```bash
docker run -d --restart=always --name=test-restart-always test-restart
```

容器退出后 docker daemon 自动重启。虽然每次生命周期只有 5 秒，但不断重启，表面上像一直在运行。

如果手动 `docker stop` 停止，不会自动重启。但重启 Docker daemon 后，容器会再次启动。

**结论：除非手动停止，否则总是重启。重启 Docker daemon 也会触发。**

## on-failure

```bash
docker run -d --restart=on-failure:3 --name=test-restart-on-failure test-restart
```

容器退出后触发重启。容器退出后触发重启。当重启达到 3 次上限后不再重启。

注意：Docker 认为容器成功启动至少需运行 10 秒。脚本 5 秒就退出，触发重启的次数会持续累加直到上限（参考[Docker 官方文档](https://docs.docker.com/config/containers/start-containers-automatically/#restart-policy-details)）。

**结论：仅在容器非正常退出时重启，可设置最大重试次数。**

## unless-stopped

```bash
docker run -d --restart=unless-stopped --name=test-restart-unless-stopped test-restart
```

表现和 `always` 一样——容器退出后自动重启。区别在于：手动 `docker stop` 停止后，重启 Docker daemon 时容器**不会**自动启动，而 `always` 会。

**结论：与 `always` 类似，但手动停止后重启 Docker 不会再次启动。**

## 如何选择

- **`no`**：一次性任务、调试环境，不需要自动重启
- **`always`**：Web 服务、数据库等需要持续运行的服务
- **`on-failure`**：脚本任务、批处理作业，只在出错时重试
- **`unless-stopped`**：和 `always` 类似，但希望手动停止后不再被 Docker 重启时使用

## 参考资料

- [了解 Docker 容器重启策略，这一篇就够了！ - 墨天轮](https://www.modb.pro/db/151597)
