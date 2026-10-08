# 墨客md · 单文件 Markdown 在线编辑器

一个**零构建、零依赖、单 HTML 文件**的 Markdown 编辑器，基于 [Vditor 3.11.0](https://github.com/Vanessa219/vditor) 引擎。双击 `index.html` 即可使用（首次加载需联网拉取 CDN 资源）。

## 功能

- **分屏 / 纯编辑 / 纯预览** 三种视图模式，支持全屏
- **导出**：Markdown / HTML / Word(.doc) / PDF（打印） / PNG 图片（html2canvas，固定 800px 宽）
- **复制**：复制源码、复制富文本预览
- **深色 / 浅色** 主题切换，自动记住选择
- **本地自动备份**：输入防抖 1.2s 自动写入 localStorage，切换标签页/关闭页面时兜底保存，下次打开自动恢复
- **降级模式**：CDN 加载失败时自动切换为纯文本编辑，核心导入导出仍可用
- 图片粘贴/上传（转 base64 内嵌）、数学公式（KaTeX）、代码高亮、任务列表、脚注、TOC

## 使用

```bash
# 直接打开
start index.html

# 或起个静态服务
python -m http.server 8000
# 浏览器访问 http://localhost:8000
```

部署到 GitHub Pages：仓库 Settings → Pages → 选择 `main` 分支根目录即可。

## 2026-10-08 修复记录

经无头浏览器自动化回路（Playwright + Edge，21 项断言）定位并验证，修复 4 个 bug：

| # | 问题 | 根因 | 修复 |
|---|------|------|------|
| 1 | **自动备份被清空（数据丢失）**：编辑器尚未初始化完成时切换标签页/关闭页面，`getMd()` 返回空串，把已有备份覆盖为空 | `visibilitychange` / `beforeunload` 处理器缺少"内容已初始化"守卫 | 新增 `contentReady` 标志，首次 `setValue` 完成前禁止写备份 |
| 2 | **翻译机制改写用户正文（数据损坏）**：正文中恰好独立成行的 `Edit`/`Desktop`/`Refresh` 等英文词被替换成中文并写进文档和备份 | UI 翻译的 TreeWalker 遍历了整个编辑器，包括源码编辑区（contenteditable）和预览区 | 翻译器跳过用户正文区（contenteditable / `.vditor-reset` / `.vditor-wysiwyg` / `.vditor-ir`），仅翻译真正的界面文案 |
| 3 | **退出全屏后视图模式被重置为分屏** | 重初始化时 `applyViewMode('split')` 写死 | 改为恢复记忆的 `currentViewMode` |
| 4 | **窗口缩放时编辑器高度不更新** | Vditor 3.11.0 没有公开的 `resize` 方法，`vditor.resize()` 全部静默抛错被吞掉 | 新增 `resizeEditorHeight()` 直接设置容器高度 |

## 已知限制

- 深色模式下预览区表格行底色仍偏亮（Vditor 内容主题未随深色切换，仅影响观感，不影响导出结构）
- 简易（降级）模式的 Markdown 转换器不支持表格语法
- 导入文件按 UTF-8 读取，GBK 等其他编码会乱码
- 依赖 jsdelivr CDN，完全离线时自动进入简易模式

## 目录

- `index.html` —— 全部内容（HTML + CSS + JS 单文件）
