# 更新日志（开发者向）

格式参考 [Keep a Changelog](https://keepachangelog.com/)，版本号遵循语义化。

## v1.1.0 — 2026-10-08

仓库改版：从单一编辑器扩展为**「个人自用的本地网页工具」**集合，新增导航首页 `index.html`；墨客md 移至 `markdown-editor.html`，三个新工具入库（重命名为 ASCII 文件名便于链接与部署）：

| 原文件 | 仓库文件 |
|--------|----------|
| （新增） | index.html |
| 墨客md·最终稳定版-3.html | markdown-editor.html |
| Base64 在线编码解码.html | base64.html |
| Favicon & ICO 图标在线生成器.html | favicon.html |
| （本地运行）极简文本处理.html | text-processor.html |

### 修复（新入库工具，均经无头浏览器 24 项断言先红后绿验证）

| 工具 | 问题 | 修复 |
|------|------|------|
| Base64 | emoji 等 4 字节 UTF-8 字符编码出非标准 Base64（代理对按两个 UTF-16 单元分别编码），且无法解码其他工具生成的标准编码 | 改用 `TextEncoder` / `TextDecoder` 标准编解码；`\u` 与 `&#` 输出按 Unicode 码点遍历（补充 `\u{...}` 形式） |
| Favicon | 断网/CDN 失败时「PNG 套装 (.zip)」按钮因 JSZip 未加载而静默失败（仅控制台报错） | 增加明确提示；另防护无固有尺寸的 SVG（`naturalWidth` 为 0 时不再除零） |
| 文本处理 | 刷新后内容全部丢失 | 增加 localStorage 自动保存（0.8s 防抖；输入与处理按钮 `updateText` 两条路径均触发），刷新/重开自动恢复 |

### 排查后确认无需修改

- 文本处理：`\r` 残留担忧不成立——textarea 的 value 消毒算法自动把 `\r\n` / `\r` 归一为 `\n`（实测验证）。
- Favicon：ICO 二进制头构造正确（`00 00 01 00` + 条目数 + 256→0 的宽高编码），PNG payload 偏移正确。
- Base64：URL-safe 符号替换、Hex/字节流输入、图片 DataURI、自动编码、快捷键均正常。

## v1.0.0 — 2026-10-08

首个公开发布版本（墨客md 单编辑器）。

## v1.0.0 — 2026-10-08

首个公开发布版本。

### 相对于内部版本「墨客md·最终稳定版-3」的修复

以下 4 项均通过无头浏览器自动化回路（Playwright + Edge，21 项断言）先红后绿验证，正向对照（导出五件套、降级模式、主题持久化、自动保存等 17 项）无回归：

| # | 问题 | 根因 | 修复 |
|---|------|------|------|
| 1 | **自动备份被清空（数据丢失）**：编辑器尚未初始化完成时切换标签页/关闭页面，`getMd()` 返回空串，把已有备份覆盖为空 | `visibilitychange` / `beforeunload` 处理器缺少"内容已初始化"守卫 | 新增 `contentReady` 标志，首次 `setValue` 完成前禁止写备份 |
| 2 | **翻译机制改写用户正文（数据损坏）**：正文中恰好独立成行的 `Edit`/`Desktop`/`Refresh` 等英文词被替换成中文并写进文档和备份 | UI 翻译的 TreeWalker 遍历了整个编辑器，包括源码编辑区（contenteditable）和预览区 | 翻译器跳过用户正文区（contenteditable / `.vditor-reset` / `.vditor-wysiwyg` / `.vditor-ir`），仅翻译真正的界面文案 |
| 3 | **退出全屏后视图模式被重置为分屏** | 重初始化时 `applyViewMode('split')` 写死 | 改为恢复记忆的 `currentViewMode` |
| 4 | **窗口缩放时编辑器高度不更新** | Vditor 3.11.0 没有公开的 `resize` 方法，`vditor.resize()` 全部静默抛错被吞掉 | 新增 `resizeEditorHeight()` 直接设置容器高度 |

### 文案调整

- 页面副标题与「介绍」区块改为面向普通用户的表述；示例文档末尾的开发者提示改为使用提示。

### 已知限制（技术细节）

- 深色模式下预览区表格行底色仍偏亮：`vditor.setTheme()` 未传内容主题参数，light content-theme 的行背景未被 `.export-style` 覆盖。仅影响观感，不影响导出结构。
- 简易（降级）模式的 `simpleMarkdownToHtml` 不支持表格语法。
- 导入文件按 UTF-8 读取（`FileReader.readAsText` 默认），GBK 等编码会乱码。
- 依赖 jsdelivr CDN；完全离线时自动进入简易模式。

### 构建 / 上传环境备注

- 仓库首推经 api.github.com Git Data API 通道完成（构建环境直连 github.com:443 不通）。
