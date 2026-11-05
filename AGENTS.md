# AGENTS.md

面向 AI 编码代理的项目说明。本文件基于对仓库实际内容的勘察编写。

## 项目概述

这是 **ten2net.github.io** —— 一个托管在 GitHub Pages 上的纯静态网站（`https://ten2net.github.io/`），站点名为「**揽宝.复盘**」（副标题「揽宝投研」）。内容由两部分组成：

1. **A 股每日复盘报告**（2026 年起的新内容）：每个交易日一篇，由外部工具「揽宝投研」生成，标题形如 `2026-11-03 每日复盘 · 情绪档位退潮`，内容是基于涨停池、连板高度、炸板率等公开数据的盘面定性分析。
2. **旧技术博客**（2016 年为主）：深度学习（keras、caffe、digits）、Docker、OpenCV、Pandoc、三维视景仿真（Vega Prime、VR-Force）等笔记，通过 tags/ 和 topics/ 两个分类法归档。

站点由 **Hugo 0.169.0** 静态站点生成器构建，使用 **hugo-theme-cleanwhite** 主题（支持明暗主题切换）。**重要：本仓库只包含 Hugo 构建后的产物，不包含 Hugo 源工程**（没有 `config.toml`、`content/`、`themes/` 或 CI 工作流）。直接编辑这里的 HTML 是既有的工作方式（例如最近的提交直接改写了 `about/index.html` 和 `index.html` 的头像与横幅）。

## 目录结构

```
index.html / 404.html          # 首页与 404 页
index.xml / sitemap.xml        # RSS 订阅源与站点地图（Hugo 生成，需保持内容一致）
post/YYYY-MM-DD-每日复盘/       # 每篇复盘一个目录，内含独立的 index.html（自包含页面）
post/index.html, post/index.xml
page/1/, page/2/               # 首页分页
tags/                          # Hugo taxonomy：每篇文章的标签归档页
topics/                        # Hugo taxonomy：主题归档页
2016/12/21/… 2026/09/23/…      # 按 年/月/日/文章名/ 组织的历史文章
about/                         # 关于页（手工定制较多）
css/ js/ fonts/ webfonts/ img/ # 主题静态资源（cleanwhite 主题 + astock.css 定制样式）
astock-deploy.json             # 「揽宝投研」部署清单（见下）
```

## 关键约定

- **`astock-deploy.json`**：由「揽宝投研」工具生成和维护的清单文件，列出了该工具部署/管理的全部文件（复盘文章页面、相关 css/js/img、page/ 分页、tags/每日复盘/、RSS 等）。**手工修改这些文件时要意识到它们可能被该工具的下一次部署覆盖**；不在清单内的文件（如 `about/`、旧的 2016 年文章、图标、topics/）则由人工维护。提交前建议确认改动文件是否在此清单中。
- **复盘文章是自包含的**：每篇就是一个目录下的单个 `index.html`（约 100KB+，内联了全部内容与图表引用），不依赖模板重新渲染。
- **发布新复盘时需要同步更新的文件**（参照 `astock-deploy.json`）：新文章目录、`post/index.html`、`index.html`（文章列表）、`index.xml`（RSS，lastBuildDate）、`page/1`、`page/2`（分页）、`tags/每日复盘/` 及其分页、`sitemap.xml`。
- **URL 使用中文路径**：文章目录名含中文（如 `post/2026-11-03-每日复盘/`），canonical 链接为 URL 编码形式。新增文章时保持同样的命名模式。
- **每篇文章的 meta 描述**是盘面定性结论的一句话摘要（og:description / twitter:description / RSS description 三处一致），这是该站点内容风格的一部分。

## 构建与部署

- 仓库内**没有可执行的构建命令**（无 package.json / pyproject.toml / Makefile / CI）。本地预览静态文件即可，例如：`python3 -m http.server 8000`。
- **部署 = 提交并推送到 `master` 分支**（`origin`: https://github.com/ten2net/ten2net.github.io.git），GitHub Pages 直接从 master 分支提供服务。
- 提交信息使用**中文**，风格为简短祈使句（如「复盘: 发布 12 篇每日复盘」「重写 about 页面：量化投研方向与技术偏好介绍」）。

## 测试

没有自动化测试。验证改动的可行方式：

1. 用浏览器或 `python3 -m http.server` 本地打开页面，确认渲染、样式（css/astock.css 与主题 css）和明暗主题切换正常。
2. 检查页面内链接（canonical、标签链接、分页链接）是否指向存在的路径。
3. 若手工改动了 RSS/站点地图，用 XML 解析器验证 `index.xml`、`sitemap.xml` 格式合法。
4. `git status` 确认没有遗留未预期的文件。

## 安全与注意事项

- 这是公开的个人站点，内容基于公开数据；复盘文章中的免责声明（样本口径、不外推全市场）是内容合规的一部分，编辑时不要删除。
- 不要引入需要构建步骤或外部服务的改动——当前架构是「生成工具 + 纯静态文件 + git 推送」，保持一致。
- 仓库中的 `.png`/`.woff` 等二进制资源与图标文件均为手工维护资产，避免无意义地重命名或移动，以免破坏既有页面引用。
