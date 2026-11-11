---
title: "{{ replace .File.ContentBaseName `-` ` ` | title }}"
date: {{ .Date }}
lastmod: {{ .Date }}
draft: true

# 分类（教程类统一放这里，方便归档）
categories: ["教程"]
tags: []

# 摘要（列表页显示）
summary: ""

# 显示目录
ShowToc: true
TocOpen: true

# 是否显示阅读时间/字数
ShowReadingTime: true
ShowWordCount: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

<!-- 在正文开头插入教程信息卡，按需填写：

{{< tutorial device="设备型号" os="系统版本" tools="所需工具" difficulty="简单/中等/困难" time="30 分钟" risk="低/中/高" >}}

-->

## 前言

这篇教程要解决什么问题、适合谁看。

## 准备工作

- 需要的软件
- 需要的硬件
- 注意事项

## 操作步骤

### 第一步

具体操作……

### 第二步

具体操作……

## 常见问题

**Q：遇到 XXX 怎么办？**

A：……

## 总结

写点收尾。
