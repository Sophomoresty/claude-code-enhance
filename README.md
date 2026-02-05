# Claude Code Enhance

为 VSCode Claude Code 扩展添加代码高亮, LaTeX 公式渲染, UI 优化, AI 对话复制等功能.

## 功能特性

- **代码语法高亮** - Highlight.js, 支持 180+ 种语言
- **LaTeX 公式渲染** - KaTeX, 支持矩阵/分数/积分等
- **AI 对话复制** - 一键复制 AI 回复内容 (不含思维链和工具调用)
- **DOM 探测工具** - Ctrl+Shift+D 导出 DOM 结构用于分析
- **表格暗色主题** - 渐变表头, 悬停高亮, 圆角边框
- **代码自动换行** - 长命令行自动换行显示
- **滚轮缩放** - Ctrl + 滚轮缩放界面 (50%-200%)
- **列表样式修复** - 有序列表数字正常显示

## 兼容版本

- Claude Code Extension: **2.1.31**
- 平台: Windows (win32-x64), Linux (linux-x64)

## 安装

### 方式一: 补丁脚本 (推荐)

```bash
cd claude-code-enhance
node patch_extension.js
```

脚本会自动:
1. 查找已安装的 Claude Code 扩展
2. 复制 enhance.js 到扩展目录
3. 修改 CSP 策略允许加载 CDN 资源
4. 注入增强脚本

### 方式二: 手动安装

1. 复制 `webview/enhance.js` 到扩展的 `webview/` 目录
2. 修改 `extension.js` 中的 CSP 策略
3. 在 HTML 模板中注入 enhance.js 脚本标签

## 安装后

重载 VSCode 窗口: `Ctrl+Shift+P` → `Developer: Reload Window`

## 使用说明

### AI 对话复制

- 鼠标悬停在 AI 回复末尾, 右下角会出现「复制」按钮
- 点击复制按钮, AI 回复内容以 Markdown 格式复制到剪贴板
- **自动排除**: 思维链 (Thinking) 和工具调用内容
- **保留格式**: 代码块, 表格, LaTeX 公式, 列表等

### DOM 探测工具

- 按 `Ctrl+Shift+D` 导出当前页面的 DOM 结构
- 自动分析消息容器, 类名, 文本内容等
- 用于开发调试和 DOM 结构分析

### 滚轮缩放

- `Ctrl + 滚轮上` - 放大
- `Ctrl + 滚轮下` - 缩小
- 缩放范围: 50% - 200%
- 缩放比例自动保存

### LaTeX 公式

| 语法 | 类型 | 示例 |
|------|------|------|
| `$$...$$` | 块级公式 | `$$\sum_{i=1}^n i$$` |
| `$...$` | 行内公式 | `$x^2 + y^2$` |
| `\[...\]` | 块级公式 | `\[\int_0^1 x dx\]` |
| `\(...\)` | 行内公式 | `\(e^{i\pi} + 1 = 0\)` |

### LaTeX 自动修复

脚本会自动修复常见的 LaTeX 语法错误:

| 错误语法 | 自动修复为 |
|----------|------------|
| `\sum{j=1}^{K}` | `\sum_{j=1}^{K}` |
| `\[6pt]` | `\\[6pt]` |
| 多行 `$...$` | 单行 `$...$` (Obsidian 兼容) |

支持的环境: `\begin{cases}`, `\operatorname`, `\text` 等。

矩阵示例:
```latex
$$
\begin{pmatrix}
1 & 2 \\
3 & 4
\end{pmatrix}
$$
```

## 扩展更新后

Claude Code 扩展更新会覆盖补丁, 重新运行即可:

```bash
node patch_extension.js
```

## 项目结构

```
claude-code-enhance/
├── patch_extension.js  # 补丁脚本
├── webview/
│   └── enhance.js      # 增强脚本 (核心)
└── README.md
```

## 技术细节

### CSP 修改

补丁脚本修改以下 CSP 策略:

- `style-src`: 添加 `https://cdnjs.cloudflare.com`
- `script-src`: 添加 `https://cdnjs.cloudflare.com`
- `font-src`: 添加 `https://cdnjs.cloudflare.com data:`

### AI 对话复制实现

由于 Claude Code 扩展的代码经过混淆, 直接修改不可行. 本项目采用 **DOM 注入 + CSS 选择器** 的方式:

1. **DOM 分析**: 使用 `[class*="timelineMessage_"]` 等模糊选择器定位元素
2. **内容过滤**: 通过类名前缀排除思维链 (`thinking_*`) 和工具调用 (`toolUse_*`)
3. **Markdown 转换**: 递归遍历 DOM, 将 HTML 转换为 Markdown 格式
4. **按钮注入**: 使用 MutationObserver 监听 DOM 变化, 动态添加复制按钮

### 外部依赖

- [Highlight.js](https://highlightjs.org/) 11.9.0 (vs2015 主题)
- [KaTeX](https://katex.org/) 0.16.9

## 开发方法论

本项目采用 **Plan-Driven Development** 模式:

1. **使用 /plan 进入计划模式** - 分析需求, 识别风险, 制定分步计划
2. **DOM 探测优先** - 使用 Ctrl+Shift+D 导出实际 DOM 结构进行分析
3. **模糊选择器** - 使用 `[class*="xxx"]` 应对混淆代码
4. **递归设计** - HTML 转 Markdown 使用递归返回字符串, 避免数组模式的换行问题

### 开发流程示例

```
1. 使用 /plan 创建实现计划
2. 修改 enhance.js 代码
3. 运行 node patch_extension.js 应用
4. 重载 VSCode 测试
5. 使用 Ctrl+Shift+D 验证 DOM 结构
6. 迭代优化
```

## License

MIT
