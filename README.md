# 个人自用的本地网页工具

一套**单文件、打开即用、完全本地运行**的网页小工具。每个工具都是一个独立 HTML 文件：双击就能用，所有数据只留在你自己的浏览器里，**不上传任何服务器**。

## 工具列表

| 工具 | 文件 | 简介 |
|------|------|------|
| 📝 墨客md · Markdown 编辑器 | [markdown-editor.html](markdown-editor.html) | 左边写、右边实时预览，一键导出 Word / PDF / HTML / 长图片，自动备份防丢稿 |
| 🔐 Base64 编码解码 | [base64.html](base64.html) | 文本与 Base64 互转，支持 Hex、字节流、URL-safe 符号替换、图片转 DataURI |
| 🎨 Favicon 图标生成器 | [favicon.html](favicon.html) | 拖入任意图片，生成多尺寸网站图标 .ico 与 PNG 套装 |
| ✂️ 极简文本处理 | [text-processor.html](text-processor.html) | 一键排版、中英标点转换、清理 HTML/JS 代码、多维字数统计，自动保存 |

打开 [index.html](index.html) 可以从导航页进入各个工具。

![墨客md 截图](screenshots/light.png)

## 怎么用

- **本地使用**：直接双击任意 `*.html` 文件（推荐 Chrome / Edge 浏览器）
- **在线部署**：整个仓库扔到任意静态空间（GitHub Pages、虚拟主机等）即可

> 提示：首次打开需联网加载编辑器组件；除 Favicon 生成器的「PNG 套装 (.zip)」需要联网加载打包组件外，其余功能基本离线可用。

## 数据安全

所有工具的正文、编码结果、自动保存内容都只写入你自己电脑的浏览器存储（localStorage），不经过任何服务器。换电脑或换浏览器不会自动同步，重要内容请用导出功能保存成文件。

## 更新日志

见 [CHANGELOG.md](CHANGELOG.md)。
