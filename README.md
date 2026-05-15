# Paper Explainer

**AI 论文交互式讲解页面生成器** / Interactive HTML Slide Generator for AI Papers

> 将 AI 领域论文转化为面向非技术背景产品经理的多页交互式 HTML 幻灯片。

---

## 概述 / Overview

**[English](#english)**
**[中文](#chinese)**

---

<a id="english"></a>

## English

### What It Does

Paper Explainer transforms AI research papers into multi-page interactive HTML slides designed for **AI product managers without a technical background**.

Each slide is a **16:9** presentation page featuring a **dual-column layout**: text explanations on the left, interactive demos on the right.

### Key Features

- **Interactive HTML slides** — Every concept comes with a visual demo or interactive widget, not just text
- **Paper-texture design** — Warm white background (`#f7f5f0`), zero external dependencies, fully self-contained HTML files
- **7-section structure** — Table of contents, background, core mechanism (expandable), architecture, design rationale, product insights, and summary
- **Progressive disclosure** — Step-by-step reveals with smooth animations to guide the reader
- **Responsive** — Works across desktop browsers and adapts to mobile viewports

### Slide Structure

| # | Section | Content |
|---|---------|---------|
| 1 | Table of Contents | Paper title + author + navigation cards |
| 2 | Background | Problem statement + limitations of prior work |
| 3 | Core Mechanism | Interactive diagrams + layman analogies + examples (expandable to 1-3 pages) |
| 4 | Architecture | System overview + component collaboration flow |
| 5 | Design Rationale | How the authors arrived at the solution, trade-offs, counter-intuitive decisions |
| 6 | Product Insights | AI product opportunities, UX redesign, tech stack choices, competitive landscape |
| 7 | Summary | One-sentence contribution + lasting impact |

### Design Principles

- **Show, Don't Tell** — Replace text-heavy descriptions with interactive visualizations
- **Visual density first** — At least 50% visual elements per screen; text never exceeds 50%
- **Layman analogies are mandatory** — Every core mechanism gets a real-life analogy, not optional decoration
- **Interaction-driven understanding** — Left column guides, right column demonstrates

### Repository Structure

```
paper-explainer/
├── SKILL.md                           # Skill definition and workflow
├── assets/
│   ├── base-slide.html                # Single-slide skeleton template
│   ├── base-template.html             # Base template
│   └── nav-inject.js                  # Navigation script (reference only)
└── references/
    ├── design-system.md               # CSS variables, layout, components, animations, responsive rules
    ├── design-spec.md                 # Design specification
    ├── gotchas.md                     # Common pitfalls and failure modes
    ├── section-writing-guide.md       # Writing framework for all 7 sections
    └── insight-writing-guide.md       # Deep writing guide for design rationale & product insights
```

### Technology

- Pure HTML + CSS + JavaScript, **zero external dependencies**
- System font stack (PingFang SC, -apple-system, BlinkMacSystemFont)
- All navigation, animations, and interactions are **fully inlined** — works offline under `file://` protocol

---

<a id="chinese"></a>

## 中文

### 功能介绍

Paper Explainer 是一个 AI 论文交互式讲解页面生成器，专为**非技术背景的 AI 产品经理**设计。

它将 AI 领域论文转化为多页交互式 HTML 幻灯片，每页采用 **16:9** 比例的**双栏布局**：左栏文字讲解，右栏交互演示。

### 核心特性

- **交互式 HTML 幻灯片** — 每个技术概念都配有可视化或交互组件，而非纯文字堆砌
- **暖白纸质感设计** — 以 `#f7f5f0` 为主背景，零外部依赖，完全自包含的 HTML 文件
- **7 大固定版块** — 目录页、论文背景、核心机制（可扩展）、整体架构、设计原理、产品洞察、总结与影响力
- **渐进式披露** — 通过"下一步"按钮逐步揭示内容，配合流畅动画引导阅读
- **响应式适配** — 支持桌面浏览器及移动端浏览

### 幻灯片结构

| # | 版块 | 内容 |
|---|------|------|
| 1 | 目录页 | 论文标题 + 作者 + 导航卡片 |
| 2 | 论文背景 | 要解决的问题 + 前人方案局限 |
| 3 | 核心机制 | 交互式图示 + 通俗讲解 + 案例（可拆分为 1-3 页） |
| 4 | 整体架构 | 架构总览图 + 组件协作流程 |
| 5 | 设计原理 | 作者如何想到解决方案、设计权衡、反直觉决策 |
| 6 | 产品洞察 | AI 产品机会、UX 重构、技术选型、竞争格局 |
| 7 | 总结与影响力 | 一句话贡献 + 后世影响 |

### 设计原则

- **Show, Don't Tell** — 用交互演示替代纯文字描述，每个技术概念必须有可视化或交互
- **视觉密度优先** — 每屏至少 50% 视觉元素（卡片/图示/动画），文字占比不超过 50%
- **通俗讲解是必需品** — 每个核心机制必须有生活类比，不是可选的附加内容
- **交互驱动理解** — 左栏放文字指引，右栏放可交互的可视化，双栏缺一不可

### 仓库结构

```
paper-explainer/
├── SKILL.md                           # 技能定义与工作流
├── assets/
│   ├── base-slide.html                # 单页幻灯片骨架模板
│   ├── base-template.html             # 基础模板
│   └── nav-inject.js                  # 导航脚本（仅参考）
└── references/
    ├── design-system.md               # CSS 变量、布局模式、组件库、动画规范、响应式规则
    ├── design-spec.md                 # 设计规范
    ├── gotchas.md                     # 常见陷阱与失败模式清单
    ├── section-writing-guide.md       # 7 个版块的写作框架
    └── insight-writing-guide.md       # 设计原理与产品洞察的深度写作指南
```

### 技术栈

- 纯 HTML + CSS + JavaScript，**零外部依赖**
- 系统字体栈（苹方、-apple-system、BlinkMacSystemFont）
- 所有导航、动画、交互均**完全内联** — 可在离线 `file://` 协议下正常运行

---

## License

MIT
