---
title: 提高你开发效率的工具 -- BrowserSync
date: 2017-01-23 10:35:56
tags: 前端工具 BrowserSync
---

**Browser-Sync**是一款前端实时可视化开发工具，之前老旧的开发环境需要修改完`code`，在浏览器按**F5**查看更新后的效果，这样一次不会有什么感觉繁琐，但是长时间的手动刷新页面，确实会浪费很多时间。

当你有了**Browser-Sync**之后，你的**F5**基本上可以退休了。

<!-- more -->
### 介绍

**Browser-Sync**是一款基于`node`的模块，这样你就可以无视**操作系统**和**浏览器**，尽情使用**Browser-Sync**。
**Browser-Sync**能让浏览器实时、快速响应您的文件更改（html、js、css、sass、less等）并自动刷新页面。

### 安装

因为**Browser-Sync**是基于`node`的模块,所以我们需要使用NPM来安装**Browser-Sync**。
```
npm install -g browser-sync
```

### 用法

用命令行工具进入你的项目目录下，建议进入需要监听的文件目录下，如果是监听根目录太浪费资源了。
```
browser-sync start --server --files "**"
```
`**`是监听该目录任意文件，你也可以指定需要监听的文件名。

这样在浏览器访问`localhost:3000/*.html`就能实时在页面中响应你代码的变化。

访问`localhost:3001`,你就会感叹`Browser-Sync` ** It's awesome !!! **

### 详情
详细说明请访问[官网](https://browsersync.io/)，配合`gulp`或者`grunt`使用更赞。