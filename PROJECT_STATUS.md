# markdown-reader 现状与交接

> 给下一次接手的人（包括换了会话的 Claude）。改这个仓库之前先读这份；改完如果改变了下面写的任何一条，回来更新。
> 最后更新：2026-10-09（按 2026-10-08 严格审查报告整改完）

## 一、基本事实
- 线上：https://fjkkx77.github.io/markdown-reader/ ，GitHub Pages 直接发 `main` 分支根目录，**push 到 main 就是上线**，没有构建步骤。
- 整个应用就是一个 `index.html`（CRLF 行尾，脚本改它时注意）。第三方库全从 CDN 加载、**全部钉死版本**。
- 测试：`MDR_PROXY=http://127.0.0.1:8800 node tests/verify.cjs`（真实 headless Chrome，手机 390 + 电脑 1280，约 100 条）。
  `node tests/verify.cjs <目录>` 可以测别的目录里的 index.html——改完拿旧版本（`git show <commit>:index.html`）跑一遍做 A/B，
  确认新加的判据在旧版上会变红。`ONLY=正则` 只跑名字匹配的用例。

## 二、改之前必须知道的约束（违反就会出事）
1. **XSS 防线**：产出 HTML 的永远是完整的 `marked.parse()`，`postprocess` 钩子里的 `DOMPurify.sanitize` 只在这条路径上生效。
   绝不能改成 `marked.lexer()+marked.parser()` 逐块渲染（实测会绕过净化）。净化配置：`ADD_ATTR:['style']`、`FORBID_TAGS:['style','form']`。
   拼进 `innerHTML` 的文件名 / 文件夹名一律 `escapeHtml`；弹框、提示条只用 `textContent`。
2. **改文档库一律走 `mutateDB(fn)`**：它先重新读最新的库、改一处、再存回。**不许**拿着早先读到的整个库对象改完整份写回——
   同时开两个标签页（或姊妹站）时会互相冲掉（审查报告 P1-2）。`storage` 事件监听在 `onExternalDBChange`。
3. **跟姊妹站共用存储**：本站和数学公式阅读器（`fjkkx77/Mathematical-formulas`，同一个 `fjkkx77.github.io` 源）共用 localStorage，
   `md-pro-db`（文档库）、`md-theme`、`md-font-scale`、`md-pro-sort` 是同一份（2026-10-09 核对过那边的源码；`md-line-numbers` 只有本站用）。
   改这些键的结构前两站一起考虑。
   本站后来新加的键都用 `mdr-` 开头：`mdr-draft:<id>`（草稿）、`mdr-read-pos`（每篇读到哪）、`mdr-last-doc`、`mdr-theme-choice`、
   `mdr-theme-migrated`、`mdr-toc-hidden`、`mdr-last-export`。
4. **换文档的入口都走 `openDoc` / `confirmLeaveEdit`**：「有未保存修改先问」只在 `confirmLeaveEdit` 一处判断。新加入口别绕开它。
5. **页面里不用原生 `alert/confirm/prompt`**：用 `ask` / `showAlert` / `confirmAsk` / `promptAsk` / `notify`（测试里有一条会扫源码）。
   唯一例外是 `beforeunload`（网页画不了那个框）。
6. **每次打开都是首页**（用户 2026-10-09 明确要求）。「接着上次读」只做成按钮：首页「继续阅读」、打开读过的文档时底部的
   「回到上次读到的位置」——**不许改成自动跳**。
7. **不做双指缩放**（用户 2026-10-09 定的）：viewport 里的 `user-scalable=no` 保留，别去改。
8. 编辑预览的光标同步：做法见记忆库 `feedback_cursor_sync_preview_recipe.md`（v2.0，`processAllTokens` 插 `.kb-block-mark` 标记块）。

## 三、2026-10-09 整改了什么（对照审查报告）
- 安全：mermaid 10.9.3→10.9.8、katex 0.16.9→0.16.22（JS/CSS 两处）、dompurify 3.4.10→3.4.16；禁 `<style>`/`<form>`；
  正文容器 `contain: layout`（行内 style 的 fixed 定位盖不住应用）。
- 数据：未保存先问（P1-1）、`mutateDB` + `storage` 监听 + 保存前检测冲突（P1-2）、备份与恢复 / 单篇下载（P1-3、O-1）、
  保存成功才更新内存（P2-4）、导入 / 新建 / 示例检查是否存上（P3-4）、数据损坏时备份没写进去就如实说并暂停保存（P3-17）、草稿（O-2）。
- 体验：Mermaid 逐张渲染出错只影响那一张（P2-1）、长公式自己滚（P2-2）、换文档回顶部（P2-5）、打字停顿再渲染（P2-6/O-5）、
  外链新标签（P3-1）、导入检查类型 + GBK（P3-3/P3-13/O-14）、编辑预览页内链接（P3-5）、目录四级缩进 / 高亮 / 可收起（P3-6/O-9）、
  阴影带 / 悬浮按钮挡字 / 行号错位 / 遮罩计时 / id 冲突（P3-7/9/10/11/12）、长名字省略号（P3-8）、示例不覆盖（P3-14/O-11）、
  快捷键（P3-15/O-4）、对比度（P3-16、首页绿按钮 #218838）、文件夹拖动方向 + 触屏长按拖（P3-18）、顶栏刘海边距 / 侧边栏高度（P3-20）、
  手机编辑 / 预览标签（O-6）、搜索（O-7）、页面内弹框（O-8）、同名导入先问（O-10）、跟随系统深浅色（O-12）、44px 触摸区域（O-13）、持久存储（O-15）。
- 没做：P3-19 双指缩放（用户决定不做）。README 改成如实描述，补了 LICENSE。

## 四、还没确认的事（需要真机 / 用户）
- iPhone 真机：①从屏幕最左边缘往右滑，打开的是侧边栏还是 Safari 的「返回」；②横屏时左上角菜单是否让开了刘海；
  ③编辑时键盘弹出后编辑区够不够大；④点外链在新标签打开后，回来是否还在原文档；⑤备份文件在 iPhone 上能否正常下载 / 选择恢复；
  ⑥手机上长按文件夹拖动排序的手感。电脑上的 headless Chrome + Playwright WebKit 都测过，但不等于 iOS。
- `navigator.storage.persist()` 能不能豁免 Safari 的 7 天清理：WebKit 没写明，不确定。真正的保险是定期导出备份。
- 姊妹站 Mathematical-formulas 用的也是 mermaid 10.9.3 / katex 0.16.9 / dompurify 3.4.10，而且它那边仍然是「整份写回」——
  两站同时开着时，**那边**仍可能冲掉这边刚存的内容。这次没改那个仓库，等用户决定。
