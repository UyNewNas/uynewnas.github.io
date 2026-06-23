2026-06-23 · 技术

---

## MeNav 是什么

MeNav 是一个轻量级的个人导航网站生成器，基于 Astro 构建。核心思路是：用 YAML 写配置，构建时生成静态 HTML，部署到 GitHub Pages（或其他静态托管）。

## 核心功能

### 分类导航

支持 2~4 层嵌套分类：

```yaml
categories:
  - name: 技术
    sites:
      - name: GitHub
        url: https://github.com
```

### 搜索引擎

内置搜索栏，Ctrl+K 唤起，支持切换多个搜索引擎（Google、Bing、GitHub 等）。

### 仪表盘

右上角实时显示时间、问候语、周数——纯前端 JS 实现。

### 明暗主题

一键切换，自动记住设置。

### Markdown 内容页

可以写本地 Markdown 文件，构建时渲染成页面。本文就是用这个功能写的。

## 适合谁用

- 想要一个**个人导航主页**的人
- 希望**分类整理**自己的书签/项目的人
- 想在 GitHub Pages 上有个**好看主页**的开发者
