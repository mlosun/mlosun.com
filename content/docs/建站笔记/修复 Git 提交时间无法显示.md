---
created: 2026-07-11
title: 修复 Git 提交时间无法显示
tags: [Hugo, 故障排查]
---

## 问题现象

文章页面底部的"最后更新"时间始终不显示，检查 Hugo 模板发现 `.GitInfo` 和 `.Lastmod` 均为空值。

## 原因

当文件路径包含中文等非 ASCII 字符时（如 `content/docs/实践笔记/Docker 重启策略详解.md`），Git 默认会对路径做转义处理，导致 Hugo 的 Git 集成无法正确匹配文件路径，`.GitInfo` 返回 nil，`.Lastmod` 随之失效。

这是 Hugo 的已知问题，umlauts（ä、ö、ü）等路径也会触发。

## 解决方案

### 本地开发

在仓库根目录执行一次：

```bash
git config core.quotePath false
```

然后删除 `.hugo_build.lock`（缓存了旧状态），重新构建即可。

### CI/CD 构建

`.cnb.yml` 中在 `hugo` 命令之前加上同一行：

```yaml
- name: Hugo 构建
  script: |
    # 文件路径包含中文等字符时会导致 Hugo 拿不到 Git 提交时间，执行以下命令修复此问题
    git config core.quotePath false
    hugo --minify
```

## 验证

构建后检查页面 HTML，如果出现 `hx:mt-12` 而非 `hx:mt-16`，说明更新日期已正常显示。

## 参考

- [GitInfo fails if there is an umlaut in the folder path — Hugo Discourse](https://discourse.gohugo.io/t/gitinfo-fails-if-there-is-an-umlaut-in-the-folder-path/32746)
- [Hugo `.GitInfo` 官方文档](https://gohugo.io/methods/page/gitinfo/)
- [Hugo `enableGitInfo` 配置](https://gohugo.io/configuration/all/#enablegitinfo)
