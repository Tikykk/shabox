# ▶ ShaBox

纯前端在线代码编辑器，零依赖，支持 HTML / CSS / JS 实时预览。

单文件 SPA，无需构建工具，部署到任意静态托管平台即可使用。

## 功能

- HTML / CSS / JS 三标签编辑器 + 实时预览
- 内置控制台：log / warn / error / info / table / clear，支持对象树展开
- 自动运行（800ms 防抖）
- 深色 / 浅色主题切换
- 代码格式化（HTML / CSS / JS）
- 导入 / 导出 HTML 文件
- 7 个内置模板（空白 / Hello World / CSS 动画 / Canvas / 表单 / 时钟 / Flexbox）
- 全屏编辑 / 预览（ESC 退出）
- 面板拖拽调整宽度
- 代码片段分享（URL Hash 编码，发链接即可打开）
- localStorage 自动保存
- 响应式布局，移动端适配
- 快捷键：`Ctrl+Enter` 运行 · `Ctrl+S` 导出 · `Ctrl+L` 清空控制台 · `Ctrl+Shift+F` 格式化

## 技术特点

- 零依赖，无框架，无构建步骤
- 单文件 SPA（~1050 行）
- `srcdoc` + `sandbox="allow-scripts"` 沙箱隔离，安全执行用户代码
- 单域名部署，兼容 GitHub Pages / Cloudflare Pages / Vercel 等任意静态托管

## 本地运行

```bash
npx serve -p 8080
# 浏览器打开 http://localhost:8080
```

## 部署

将 `index.html` 部署到任意静态托管平台即可：

- **GitHub Pages**：Settings → Pages → 选择分支和目录
- **Cloudflare Pages**：连接 GitHub 仓库，自动部署
- **Vercel**：导入仓库，零配置部署

## 项目结构

```
index.html   → 编辑器主页（单文件 SPA）
nginx.conf   → Nginx 部署配置示例
```

## 许可

MIT

## 作者

tiki
