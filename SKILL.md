---
name: paper-explainer
description: AI 论文交互式讲解页面生成器。将 AI 领域论文转化为面向非技术背景产品经理的多页交互式 HTML 幻灯片。每页一个 16:9 幻灯片，左栏讲解 + 右栏交互演示。触发条件：(1) 用户提供论文标题/链接并要求讲解、解读、分析 (2) 用户说"帮我讲讲这篇论文"、"论文解读"、"论文讲解"、"解释论文" (3) 用户要求将论文内容做成交互式网页/HTML页面/幻灯片。受众：AI 产品经理，无需技术背景。输出：多文件幻灯片组 + 目录页，暖白纸质感设计。
---

# Paper Explainer

将 AI 论文转化为多页交互式 HTML 幻灯片，每页 16:9 比例，左栏讲解 + 右栏。

## 设计定位

**受众**：AI 产品经理，无需技术背景。

**风格**：暖白纸质感（`--bg: #f7f5f0`），不是冰冷的深色终端。完整 CSS 变量、组件库、动画规范见 [design-system.md](references/design-system.md)。

**核心原则**：
- **Show, Don't Tell**：用交互演示替代纯文字描述。每个技术概念必须有可视化或交互，不能只靠文字解释
- **视觉密度优先**：每屏至少 50% 视觉元素（卡片/图示/动画），文字占比不超过 50%
- **通俗讲解是必需品，不是装饰**：每个核心机制必须有生活类比，不是可选的附加内容
- **交互驱动理解**：左栏放文字指引，右栏放可交互的可视化，双栏缺一不可

## 页面结构（7 个 section）

每页幻灯片对应一个 section。顺序固定，不可跳过：

| # | Section | 核心内容 | 必选 | 拆分 |
|---|---------|---------|------|------|
| 1 | 目录页 | 论文标题 + 作者 + 所有 section 导航卡片 | 是 | 否 |
| 2 | 论文背景 | 要解决的问题 + 前人方案局限 | 是 | 否 |
| 3 | 核心机制 | **重点**：交互式图示 + 通俗讲解 + 案例 | 是 | 1-3 页 |
| 4 | 整体架构 | 架构总览图 + 组件协作流程 | 是 | 否 |
| 5 | 设计原理 | 作者如何想到解决方案、设计权衡、反直觉决策 | 是 | 否 |
| 6 | 产品洞察 | AI 产品机会、UX 重构、技术选型、竞争格局、风险 | 是 | 否 |
| 7 | 总结与影响力 | 一句话贡献 + 后世影响 | 是 | 否 |

> 核心机制 section 可根据论文复杂度拆分为多个幻灯片页面。

写作框架和内容要求见 [section-writing-guide.md](references/section-writing-guide.md)，设计原理和产品洞察的深度写作指南见 [insight-writing-guide.md](references/insight-writing-guide.md)。

## 工作流

### 第 1 步：论文研究

获取论文核心信息：标题、作者、年份、机构、要解决的问题、核心技术方案、实验结果、后续影响。

信息来源优先级：用户提供 > deep-research > search。

### 第 2 步：内容规划

确定幻灯片数量和每页内容大纲。为每个核心机制概念选择交互方式，选择指南见 [content-philosophy.md](references/content-philosophy.md)。

### 第 3 步：生成交互式 HTML

按 [design-system.md](references/design-system.md) 规范生成。生成前必须先通读 [content-philosophy.md](references/content-philosophy.md) 和 [gotchas.md](references/gotchas.md) 避免常见陷阱。

关键规则：
1. **每页独立 HTML 文件**，文件名格式 `s01-background.html`、`s02-mechanism-a.html` 等
2. **生成目录页** `index.html`，包含所有幻灯片链接
3. **内联导航脚本**：在每个页面 `</body>` 前内联导航代码（含 `SLIDE_ORDER` + 底部浮动导航条 + 键盘/触摸事件）。**禁止**使用 `<script src="nav-inject.js">` 外部引用，`file://` 协议下会加载失败导致导航完全失效。详见 [gotchas.md](references/gotchas.md) 第 1 条
4. **CSS 变量统一**：所有页面使用相同的 `:root` 变量集
5. **页面结构统一**：`.slide` > `.hd`(header) + `.body`(左+右)
6. **直接在交付目录生成**：所有 HTML 文件必须**直接生成到**交付目录，不要先生成到灵犀工作区（`lingxi-claw`）再复制。使用 `os.makedirs(exist_ok=True)` 确保目录存在，`open()` 直接写到目标路径。论文简称从标题中提取英文关键词（如 ReAct → `react-explainer`，Chain-of-Thought → `cot-explainer`）

7. **禁止 `\uXXXX` Unicode 转义**：所有中文内容（包括 JS 中的字符串、HTML 文本、CSS content 等）一律使用 UTF-8 原生中文字符。绝不能写 `"\u505a\u68a6"` 而应直接写 `"做梦"`。这是硬性规则，违反会导致用户看到乱码。详见 [gotchas.md](references/gotchas.md) 第 8 条

### 第 4 步：质量检查

生成完成后逐项检查 [gotchas.md](references/gotchas.md) 中的常见陷阱（**尤其是第 1 条导航脚本和第 8 条 `\uXXXX` 转义问题**），然后对照 [section-writing-guide.md](references/section-writing-guide.md) 末尾的质量检查清单验证。

**必须执行的自动化扫描**：生成后用 Python 正则扫描所有 HTML 文件，检测 `\uXXXX` 残留并自动替换为 UTF-8 原生字符（参考 gotchas.md 第 8 条的验证脚本）。确认零残留后再交付。

### 第 5 步：交付

HTML 文件已在生成阶段直接写入交付目录，无需额外复制操作。仅生成 `.html` 文件即可，因为导航代码已完全内联。

告知用户：交付目录路径、幻灯片总数、导航方式（底部浮动导航条 + 键盘 Cmd+左右箭头 + 触摸滑动）、建议从 `index.html` 开始浏览。用 `file:///` 协议链接指向交付目录中的 `index.html`。

## 内容哲学

**必须在第 3 步生成 HTML 之前阅读** [content-philosophy.md](references/content-philosophy.md)，它定义了：

- **视觉密度规则**：每屏至少 50% 视觉元素
- **文本限制**：每个内容块最多 2-3 句，绝不超 4 行
- **视觉转换规则**：列表→卡片、步骤→流程图、对话→聊天气泡
- **通俗讲解（Analogy）规范**：日常生活场景，先描述 2-3 句再映射回技术
- **交互方式选择**：不同技术概念类型对应的最佳交互方式

## 常见陷阱

**必须在第 3 步生成 HTML 之前阅读** [gotchas.md](references/gotchas.md)。该文件列出了高频失败模式，按严重程度排序：

| # | 陷阱 | 严重程度 |
|---|------|---------|
| 1 | 导航脚本必须内联，禁止外部 JS 引用 | **致命** |
| 2 | 文字墙：文字占比超过 50% | 高 |
| 3 | 缺失交互：右栏无可交互元素 | 高 |
| 4 | Analogy 滥用或缺失 | 中 |
| 5 | 暗色主题混用 | 中 |
| 6 | 外部字体依赖 | 低 |
| 7 | 响应式适配缺失 | 低 |
| 8 | JS/HTML 中使用 `\uXXXX` 转义表示中文（显示乱码） | 高 |

> **特别注意**：`assets/nav-inject.js` 仅作参考学习，**绝对不要**在生成的 HTML 中通过 `<script src>` 引用。必须将导航逻辑完全内联。

## 资源

### references/（按需阅读）
- **design-system.md** — CSS 变量、布局模式、组件库、动画规范、响应式。生成 HTML 时必读
- **content-philosophy.md** — 内容创作哲学：视觉密度、文本限制、Analogy 规范、交互选择。生成 HTML 前必读
- **section-writing-guide.md** — 7 个 section 的写作框架、内容要求、质量检查清单
- **insight-writing-guide.md** — 设计原理和产品洞察 section 的深度写作指南
- **gotchas.md** — 常见失败模式清单，生成 HTML 前必读

### assets/（直接使用，不读入上下文）
- **base-slide.html** — 单页幻灯片骨架模板（含 header + 左右双栏 + CSS 变量 + 响应式）
- **nav-inject.js** — 底部浮动导航脚本（仅供参考学习设计思路，**禁止在生成的 HTML 中通过 `<script src>` 引用**，详见 gotchas.md 第 1 条）

> **注意**：
> - `assets/base-slide.html` 和 `assets/base-template.html` 可直接复制到输出目录使用，不需要读入上下文
> - `assets/nav-inject.js` **仅作参考**，不要在生成的 HTML 中引用（必须内联导航代码）
> - `references/` 中的文件按需读取，但 **gotchas.md 和 design-system.md 是必读的**