# ShaBox by tiki

纯前端在线代码编辑器，零依赖，支持 HTML/CSS/JS 实时预览。

## 文件结构

```
index.html      → 主站编辑器 (~1050行, 单文件 SPA)
nginx.conf      → Nginx 单域名部署配置 (~30行)
CLAUDE.md       → 项目文档
```

## 架构

- 单域名方案，通过 `srcdoc` 属性直接渲染预览内容，无需 preview.html
- `index.html` 中 `buildSrcdoc(html, css, js)` 组装完整 HTML 文档，设置 `previewFrame.srcdoc` 触发渲染
- 每次设置 `srcdoc` 浏览器创建全新文档，获得干净 JS 上下文
- console 拦截脚本注入 srcdoc，用 `parent.postMessage` 回传日志（单层 iframe）
- `<iframe sandbox="allow-scripts">` 防御加固，阻止访问父页面 DOM
- srcdoc iframe 的 origin 为字符串 `"null"`，message 监听器据此校验来源

## postMessage 协议

| 方向 | type | 用途 |
|------|------|------|
| 预览→主站 | `__console__` | 回传 console 输出 (level/args) |
| 预览→主站 | `__exit_fullscreen__` | ESC 退出全屏 |

## 功能清单

| 功能 | 状态 |
|------|------|
| 三标签编辑器 (HTML/CSS/JS) + 实时预览 | ✅ |
| 控制台：log/warn/error/info/table/clear，对象树展开 | ✅ |
| 自动运行 (800ms 防抖) | ✅ |
| 深色/浅色主题切换 | ✅ |
| 代码格式化 (HTML/CSS/JS) | ✅ |
| 导入/导出 HTML 文件 | ✅ |
| 7 个内置模板 (空白/Hello World/CSS动画/Canvas/表单/时钟/Flexbox) | ✅ |
| 全屏编辑/预览 (ESC 退出) | ✅ |
| 面板拖拽调整宽度 | ✅ |
| localStorage 持久化编辑内容和设置 | ✅ |
| 快捷键：Ctrl+Enter / Ctrl+S / Ctrl+L / Ctrl+Shift+F | ✅ |
| 响应式布局 (≤768px 面板切换, ≤480px 紧凑模式) | ✅ |
| 代码片段分享 (URL Hash, btoa/atob 编码) | ✅ |
| 移动端面板切换 (编辑器/预览/控制台 全屏切换) | ✅ |
| 移动端"更多"底部菜单 (导出/导入/格式化/清空/自动运行) | ✅ |
| 移动端模板菜单底部弹出 + 遮罩层 | ✅ |
| 署名 (meta/OG/favicon/logo/statusbar/导出注释) | ✅ |

## 本地测试

```bash
npx serve -p 8080   # 单服务启动
# 浏览器打开 http://localhost:8080
```

## 注意事项

- `runCode(force)` 有脏检查，代码未变时跳过运行
- message 监听器校验 `e.origin !== 'null'`，仅接受 srcdoc iframe 消息
- consoleScript 预构建一次，注入 srcdoc，不随每次运行重复拼接
- `buildSrcdoc` 对 `</style>` 和 `</script>` 做转义，防止提前闭合
- 导入 HTML 时用 DOMParser 解析，分离 style/script/body
- 全屏退出：纯视觉提示按钮（右上角弧形标签），ESC 键退出
- 控制台批量渲染 (rAF + DocumentFragment)，最多保留 500 条
- 对象树渲染最大深度 5 层，防止深嵌套导致栈溢出
- input 事件用 `requestAnimationFrame` 节流，避免每次击键都触发逻辑
- `saveToStorage` 有 300ms 防抖，关键操作 (运行/导出) 用 `saveToStorageNow` 立即保存
- 格式化用 `setTimeout` 延迟执行，先更新 UI 状态再处理
- nginx.conf 已配置 HTTPS/SSL + HTTP→HTTPS 重定向

## 错误记录

- ~~preview.html `document.open/write/close` 销毁 message 监听器，导致后续运行无法更新预览~~ → 已修复：改用内嵌 iframe 渲染
- ~~预览全屏退出按钮被 iframe 遮挡无法点击~~ → 已修复：改为纯视觉提示，ESC 键退出
- ~~runner iframe 重复 `document.write` 导致 `const/let` 重复声明报错~~ → 已修复：srcdoc 每次创建全新文档
- ~~预览 iframe 高度为 0 白屏~~ → 已修复：`preview-frame-wrap` 改为 `position:relative` + iframe `position:absolute;inset:0`
- ~~console 输出无上限导致 DOM 爆炸~~ → 已修复：限制最多 500 条
- ~~对象树递归无深度限制可能栈溢出~~ → 已修复：最大深度 5 层
- ~~saveToStorage 每次 input 都同步写入~~ → 已修复：300ms 防抖
- ~~格式化同步阻塞主线程~~ → 已修复：setTimeout 延迟执行
- ~~input 事件每次击键都触发 saveToStorage + autoRun 逻辑~~ → 已修复：requestAnimationFrame 节流
- ~~console 消息逐条 DOM 操作触发多次重排~~ → 已修复：rAF + DocumentFragment 批量渲染
- ~~clearConsole 无条件重写 DOM~~ → 已修复：加脏检查 + 清理批量队列
- ~~consoleScript 每次运行都重复拼接~~ → 已修复：预构建一次
- ~~autoRun 代码未变时重复运行~~ → 已修复：runCode 加脏检查，force 参数控制强制运行
- ~~移动端 toolbar 按钮过多挤压换行~~ → 已修复：次要按钮隐藏，收入"更多"底部菜单
- ~~移动端模板菜单被 toolbar overflow 裁剪~~ → 已修复：fixed 定位底部弹出 + 遮罩层
- ~~移动端编辑器/预览 50/50 分割空间不足~~ → 已修复：改为全屏面板切换模式
- ~~移动端预览区域不显示内容~~ → 已修复：`.main` 加 `overflow:hidden`，面板 absolute 定位撑满
- ~~移动端自动运行按钮无状态提示~~ → 已修复：显示"自动运行：开/关"
- ~~移动端 editor fullscreen-hint/exit 未隐藏~~ → 已修复：全局 `display:none!important`
- ~~移动端 textarea font-size<16px 导致 iOS 自动缩放~~ → 已修复：改为 16px
- ~~移动端 toolbar 按钮溢出~~ → 已修复：加 `overflow-x:auto`
- ~~移动端 console-panel 无滚动区域~~ → 已修复：加 `display:flex` + consoleList `overflow-y:auto`
- ~~移动端模板菜单被 toolbar overflow 裁剪不显示~~ → 已修复：template-menu/overlay 移至 body 层级，桌面端动态定位，移动端底部弹出
- ~~双域名部署限制免费静态托管~~ → 已修复：迁移至 srcdoc 单域名方案，可部署到任意静态托管平台
