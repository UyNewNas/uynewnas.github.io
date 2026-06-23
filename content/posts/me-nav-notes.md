2026-06-23 · 技术

---

之前我的 GitHub Pages 主页只是一个重定向，访问 `uynewnas.github.io` 会直接跳到 `http://36.26.81.138:8090/`。前两天终于决定把它改造成一个像样的导航站。

## 选型

搜了一圈个人主页方案，最终选了 **MeNav**：

- 基于 Astro，纯静态，构建产物只有 HTML/CSS/JS
- YAML 配置，不需要写代码
- 支持分类导航、书签导入、多层级分组
- 内置搜索引擎、明暗主题、SPA 路由
- 一键部署到 GitHub Pages

## 迁移过程

1. 清空仓库，引入 MeNav 源码
2. 配置 `config/user/site.yml`（标题、个人信息、主题等）
3. 创建 `common.yml` 导航页，按"项目/工具/AI 平台"分类
4. 创建 `projects.yml` 项目展示页
5. 添加 GitHub Actions 工作流，自动构建部署

## 遇到的小坑

- YAML 中带冒号的描述要加引号：`description: 'Arknights: Endfield Wiki'`
- 默认工作流监听 `main` 分支，我的仓库是 `master`，要改一下配置
- favicon.ico 要放在仓库根目录，构建时会自动复制到 public

## 最终效果

现在主页是一个深色风格的导航站，左侧有侧边栏，右侧是分类卡片。还有实时时钟、问候语、搜索引擎集成这些小功能。

目前已经加上了"笔记"页和"博客"页，后续打算继续完善内容。
