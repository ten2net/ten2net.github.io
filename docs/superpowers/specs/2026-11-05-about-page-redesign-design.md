# About 页重设计：终端 Hero · 现代极客风

日期：2026-11-05
状态：已获用户批准
改造对象：`about/index.html`（手工维护的独立页面，非 Hugo 生成，不受 astock-deploy.json 管理）

## 背景与目标

现 about 页已是独立深色卡片风格，用户希望重做为「现代风格、体现极客风格」的 about 页。
经澄清确定的方向：**终端/TUI 美学 + 轻交互 + 明暗双主题 + 允许视觉与结构重排**，
在三个候选方案（整页终端 / 终端 Hero + 常规内容 / TUI 双栏工作台）中选定 **方案 B：终端 Hero + 常规内容**。

目标：首屏以终端窗口建立强烈的极客第一印象；内容区保持现代可读性；
保留现有全部文案内容（自我介绍、6 个项目、技术栈、时间线、投研观、联系方式）。

## 整体架构

- 单文件 `about/index.html`，完全自包含：内联 `<style>` + 少量原生 JS，无框架、无构建、无外部 JS 依赖。
- 字体沿用 Google Fonts：Noto Sans SC + JetBrains Mono。
- 页面结构：终端 Hero → ticker 状态条 → 内容版块流 → 页脚。
- 不引入任何构建步骤或外部服务，符合仓库「生成工具 + 纯静态 + git 推送」架构。

## 终端 Hero

- 整个首屏是一个终端窗口：圆角、深色窗体、窗口栏（红黄绿三点 + 标题 `guest@ten2net: ~/about`）。
- 窗口内打字机逐行「开机」：

  ```
  $ ssh guest@ten2net.lab
  $ whoami
    揽宝 · ten2net —— A 股量化投研 × AI Agent 工程化
  $ cat motto.txt
    以数据为锚 · 以纪律为帆  (Disciplines over predictions)
  ```

- 打字机只播放一次（进入视口触发），末尾保留闪烁光标。
- 硬币头像（`/img/avatar.png`）保留为终端窗口旁的浮动徽章（coin-float 动画延续）。
- K 线柱条保留在 hero 右侧（移动端隐藏，沿用现有断点策略）。
- 窗口下方接 ticker 状态条（research / agents / infra，现有文案保留）。

## 内容版块（重排，文案全部保留）

单列内容流；各版块标题做成终端提示符样式（等宽 `$ 命令` + 路径高亮），版块间由命令语义串联：

| 提示符 | 版块 | 呈现 |
|---|---|---|
| `$ cat ./now.md` | 我在做什么 | 介绍卡片 |
| `$ ls ~/projects/` | 6 个项目 | 卡片网格，badge 改为终端风标签 `[FAVORITE]` `[RESEARCH]` `[EXPERIMENT]` `[TOOLING]` `[INFRA]` |
| `$ cat stack.config` | 技术偏好 | chips |
| `$ history \| tail -3` | 一路走来 | 时间线 |
| `$ cat beliefs.md` | 投研观 | 引言块 |
| `$ open links` | 找到我 + 返回首页 | 链接组 |

卡片本体为现代暗色玻璃质感：细边框、hover 浮起 + 边框高亮；终端元素仅作点缀，保证长文可读性。

## 明暗双主题

- CSS 变量双套配色；默认跟随系统（`prefers-color-scheme`）。
- 右上角手动切换按钮（太阳/月亮图标），`localStorage` 记忆选择，切换按钮图标随主题变化。
- 深色：近黑底 `#0d1117` + 绿 `#3fb950`/青 accent（终端正统配色）。
- 浅色：「白纸终端」——浅灰纸面底、深色文字、accent 彩色保留；终端窗口反转为浅底深字。

## 交互（轻量，原生 JS < 5KB）

1. Hero 打字机开机（IntersectionObserver 触发一次）。
2. 终端光标闪烁（CSS animation）。
3. 内容版块滚动渐入（IntersectionObserver + CSS transition）。
4. 卡片 hover 浮起与边框高亮（纯 CSS）。
5. 主题切换（读写 `localStorage`，切换 `<html>` 上的 data 属性）。

无外部依赖；JS 失败时页面内容完整可读（打字机内容在 DOM 中静态存在，JS 仅增强）。

## 边界与非目标

- 不改 `index.html` 首页、不改任何复盘文章与 astock-deploy.json 管理的文件。
- 不新增图片资源（复用 `/img/avatar.png`）。
- 不做可输入终端、不做页面间路由、不做构建工具链。
- 内容文案全部沿用现有版本，仅因版块重排做位置移动。

## 验证

1. `python3 -m http.server` 本地预览：
   - 打字机播放一次、光标闪烁、滚动渐入正常；
   - 手动切换与 `prefers-color-scheme` 自动跟随均正确；
   - 明暗两套配色下全部版块可读、终端窗口配色正确反转；
   - 页面内链接（GitHub、返回首页）与 `/img/avatar.png` 引用无 404。
2. 移动端宽度（< 720px）检查布局降级正常。
3. `git status` 确认除设计文档与 about 页外无多余产物。
