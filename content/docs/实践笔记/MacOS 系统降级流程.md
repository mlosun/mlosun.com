---
created: 2025-07-28
title: MacOS 系统降级流程
tags: [MacOS]
---
本文介绍如何将 Intel 芯片的 MacOS 从 13.x 版本降级到 10.15 或 12.x 版本（已实测），其他版本仅供参考。

> [!warning] 重要提示
> 降级过程会格式化全部磁盘内容，请务必提前备份所有重要数据，并在多处进行备份！

## 参考链接

* 视频教程：[经验分享｜macOS｜如何创建可引导的macOS安装器并重装macOS系统](https://www.bilibili.com/video/BV1RY4y1H7uZ/)
* 官方文档：[创建可引导的 macOS 安装器 - 官方 Apple 支持 (中国)](https://support.apple.com/zh-cn/101578)
* 官方文档：[如何下载和安装 macOS - 官方 Apple 支持 (中国)](https://support.apple.com/zh-cn/102662)

## MacOS 版本推荐

以下版本信息针对 2017 MacBook Pro 设备：

* Ventura 13——最后一个支持的版本
* Monterey 12——稳定性一般，兼容性较好
* Catalina 10.15——稳定性最佳，兼容性一般

## 操作步骤

### 1. 准备工作

1. 备份所有重要数据
2. 退出系统的 Apple ID
3. 准备一个 14GB 以上的 USB 闪存盘

### 2. 下载 MacOS

1. 在 [本页面](https://support.apple.com/zh-cn/102662) 通过 App Store 下载所需的系统版本（如 [Monterey 12](macappstores://apps.apple.com/cn/app/macos-monterey/id1576738294?mt=12)）
2. 下载完毕后，若自动打开安装程序，直接关闭退出
3. 确保应用程序文件夹内有名为 `安装macOS Monterey` 的安装器

### 3. 创建 macOS 安装器

1. 插入准备的 USB 闪存盘
2. 使用系统自带的 `磁盘工具` 应用，将其全盘抹掉并格式化：
   * 名称：`MyVolume`（建议使用此名称，方便后续操作）
   * 格式：`Mac OS拓展（日志式）`（是否区分大小写不影响）
3. 启动终端，根据所需的系统版本输入命令（[命令来源](https://support.apple.com/zh-cn/101578)）：
   * Catalina：`sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume`
   * Monterey：`sudo /Applications/Install\ macOS\ Monterey.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume`
4. 根据终端提示完成安装：
   * 输入登录密码（输入时终端无字符提示）
   * 输入 `Y` 确认全部抹掉 USB 闪存盘（弹出允许终端访问可移动宗卷的提示时点击允许）
   * 耐心等待，过程较为漫长
5. 安装完成后，USB 闪存盘名称将改为类似 `Install macOS Monterey` 的名称

### 4. 启动 MacOS 安装程序

1. 重启设备后，立刻按住 `Option` 键，直到出现可引导安装器的宗卷（即 `Install macOS Monterey`）
2. 通过方向键选择安装器（若无法启动，需确保“启动安全性实用工具”已设为允许从外部或可移动介质启动）

### 5. 安装 MacOS

1. 进入安装器后，启动磁盘工具，抹掉内置的整个硬盘（通常被命名为 `Macintosh HD`）：
   * 名称：`Macintosh HD`
   * 格式：`APFS`
2. 关闭磁盘工具，返回选择 `安装macOS Monterey`
3. 选择安装到 `Macintosh HD`
4. 耐心等待，过程较为漫长

### 6. 完成安装

安装完成后，剩余步骤为初始化新的 MacOS 系统流程。
