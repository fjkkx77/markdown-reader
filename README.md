# Markdown Reader ⚡️
> 开箱即用的 Markdown 阅读 / 编辑器：打开本地 .md 文件就能看，文档存在浏览器本地，无广告、无追踪、不用注册。

[![GitHub stars](https://img.shields.io/github/stars/fjkkx77/markdown-reader?style=social)](https://github.com/fjkkx77/markdown-reader/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/fjkkx77/markdown-reader?style=social)](https://github.com/fjkkx77/markdown-reader/network/members)
[![在线使用](https://img.shields.io/badge/在线使用-点击访问-brightgreen)](https://fjkkx77.github.io/markdown-reader/)

---

## ✨ 功能
- 📖 **阅读**：标题目录（读到哪一节自动高亮）、字号调节、深浅色（默认跟随系统）、代码高亮与一键复制、可选代码行号
- 🧮 **公式、图表、脚注**：KaTeX 公式（中文紧贴着写也能识别）、Mermaid 流程图 / 时序图、脚注、GitHub 风格提示框（`> [!NOTE]`）、表格、任务列表
- ✏️ **编辑**：电脑上左右分栏实时预览，光标移到哪一行、预览就定位到哪；手机上「编辑 / 预览」两个标签切换。`Ctrl/⌘+S` 保存，`Tab` 缩进
- 💾 **草稿自动保存**：编辑时停顿一下就把草稿存在本地，误关页面、手机杀后台后再打开这篇，会提示恢复（正式保存仍需点「保存」）
- 🔖 **接着上次读**：每次打开是首页；首页有「继续阅读」，打开读过的长文档时底部会出现「回到上次读到的位置」，点了才跳
- 🗂️ **文档管理**：文件夹、置顶、拖动排序（手机上长按拖）、按标题或内容搜索、按时间排序
- 📤 **导入导出**：导入 .md / .txt（UTF-8 和 GBK 编码都认）；单篇下载为 .md；整库导出为一个备份文件，随时恢复（合并或替换）
- 📱 **手机友好**：按 iPhone 的交互习惯做（底部操作菜单、居中确认框、44px 触摸区域），窄屏不横向溢出

> ⚠️ 文档只存在**当前这个浏览器**里：换设备、清除浏览器数据都会丢；iPhone 的 Safari 还会在连续 7 个使用 Safari 的日子里没打开过本站时自动清空本站数据。请定期用左上角菜单的「备份与恢复」导出一份。

---

## 🖼️ 项目预览
<img width="1280" height="651" alt="PixPin_2026-04-01_08-22-00" src="https://github.com/user-attachments/assets/1c933dad-0f30-4155-b5b5-4a484d263ea6" />
<img width="1280" height="654" alt="PixPin_2026-04-01_08-22-21" src="https://github.com/user-attachments/assets/db91c3b2-e7bd-48b1-a042-fc1bec577ce9" />
<img width="1280" height="651" alt="PixPin_2026-04-01_08-22-40" src="https://github.com/user-attachments/assets/c53da28c-698e-4aae-b84b-b99a6f926d69" />
<img width="1280" height="653" alt="PixPin_2026-04-01_08-23-00" src="https://github.com/user-attachments/assets/78f38eeb-83b8-4d99-9b1a-ec567812f70e" />
<img width="1280" height="652" alt="PixPin_2026-04-01_08-23-22" src="https://github.com/user-attachments/assets/ddd32cb5-9c5d-4ccc-b39e-8627e76324a4" />

（截图是 2026-04 的版本，界面细节以线上为准。）

---

## 🚀 快速开始
### 方式一：直接在线使用（推荐）
打开 [fjkkx77.github.io/markdown-reader](https://fjkkx77.github.io/markdown-reader/) 即可使用。手机上建议「添加到主屏幕」。

### 方式二：部署到自己的 GitHub Pages
1. 点击页面右上角的 **Fork**，把本仓库复刻到你自己的 GitHub 账号下
2. 进入你复刻后的仓库 → `Settings` → 左侧 `Pages`
3. Source 选 `Deploy from a branch`，Branch 选 `main`，文件夹选 `/ (root)`，点 Save
4. 等一两分钟刷新，就能看到你自己的访问链接

### 方式三：本地运行
下载仓库，用浏览器直接打开 `index.html`（第三方库从 CDN 加载，需要联网）。

---

## 🛠️ 技术栈
- 单个 `index.html`，原生 HTML + CSS + JavaScript，没有构建步骤
- Markdown 渲染：[marked](https://marked.js.org/)（+ marked-highlight、marked-footnote、marked-alert）
- 代码高亮：[highlight.js](https://highlightjs.org/)
- 公式：[KaTeX](https://katex.org/)；图表：[Mermaid](https://mermaid.js.org/)
- 安全：所有渲染结果经 [DOMPurify](https://github.com/cure53/DOMPurify) 净化，文档里的脚本、`<style>`、表单不会生效
- 第三方库全部钉死版本（升级要整组一起测，并定期查这些版本有没有新公开的漏洞）

## 🧪 测试
```bash
# 页面的库从国外 CDN 加载，headless Chrome 不走系统代理，用 MDR_PROXY 指定一个
MDR_PROXY=http://127.0.0.1:8800 node tests/verify.cjs
```
用真实的 headless Chrome（手机 390 宽 + 电脑 1280 宽）逐条验证，需要本机装有 Chrome 和 Node 22+。

---

## 📝 更新日志
### 2026-10-09
- 安全：Mermaid 升到 10.9.8（修复时序图标签能执行代码的 CVE-2025-54881 等 8 条公开漏洞）、KaTeX 升到 0.16.22、DOMPurify 升到 3.4.16；文档里的 `<style>`、`<form>` 不再生效
- 数据：编辑中换文档 / 新建会先确认；同时开两个标签页不再互相覆盖；新增整库备份与恢复、单篇下载 .md、草稿自动保存
- 新增：继续阅读 / 回到上次位置、侧边栏搜索、手机编辑 / 预览标签、目录高亮当前节、快捷键、页面内确认框
- 修复：长公式撑宽页面、Mermaid 写错时目录不更新、换文档沿用旧滚动位置、外链在当前页打开、GBK 文件乱码等 20 余处

### v1.0.0 (2026-03-31)
- 初始版本

---

## 🤝 贡献
发现 Bug 或有功能建议，欢迎提交 [Issue](https://github.com/fjkkx77/markdown-reader/issues)；想贡献代码，Fork 后提交 PR 即可。

---

## 📄 开源协议
本项目采用 [MIT 协议](LICENSE) 开源。
