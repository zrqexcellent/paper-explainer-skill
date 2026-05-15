# 交互设计规范

## 设计原则

- **暗色主题**：深色背景减少视觉疲劳，适合长篇阅读
- **渐进披露**：术语悬浮展开、Tab 切换、点击交互，避免信息过载
- **通俗优先**：每个技术概念配生活类比（analogy），让非技术读者秒懂
- **交互驱动理解**：用可视化让抽象概念可感知，而非纯文字描述

## CSS 变量体系

```css
:root {
  --primary: #4f46e5;
  --primary-light: #818cf8;
  --accent: #f59e0b;
  --bg: #0f172a;
  --bg-card: #1e293b;
  --bg-card2: #334155;
  --text: #e2e8f0;
  --text-dim: #94a3b8;
  --green: #10b981;
  --red: #ef4444;
  --blue: #3b82f6;
  --pink: #ec4899;
  --cyan: #06b6d4;
  --orange: #f97316;
  --radius: 12px;
}
```

> 根据论文主题可微调色板（如 CV 论文偏暖色、NLP 论文偏冷色），但必须保持暗色基调。

## 页面结构

```
<body>
  <nav class="side-nav">
    <a href="#s1" title="标题1"></a>  <!-- 每个 section 一个锚点 -->
    ...
  </nav>
  <div class="container">
    <section class="hero">...</section>         <!-- 顶部 Hero 区域 -->
    <section class="section fade-in" id="s1">...</section>
    <section class="section fade-in" id="s2">...</section>
    ...
  </div>
</body>
```

## 核心组件

### 1. Hero 区域

页面顶部的论文标题区。渐变背景 + 大标题 + 一段话概述。

```css
.hero {
  min-height: 60vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  background: linear-gradient(135deg, #0f172a 0%, #1e1b4b 50%, #0f172a 100%);
  padding: 2rem;
}
.hero h1 { font-size: clamp(2rem, 5vw, 3.5rem); color: #fff; }
.hero .subtitle { color: var(--text-dim); max-width: 700px; margin-top: 1rem; font-size: 1.1rem; line-height: 1.8; }
```

### 2. Card 卡片

承载内容的主体容器。

```css
.card {
  background: var(--bg-card);
  border-radius: var(--radius);
  padding: 1.5rem;
  margin-bottom: 1.5rem;
  border: 1px solid rgba(255,255,255,0.06);
}
.card h3 { color: var(--accent); margin-bottom: 0.8rem; font-size: 1.15rem; }
```

### 3. Term-Box 术语悬浮

行内术语，hover 弹出解释气泡。每页至少 10 个。

```html
<span class="term-box">术语名<span class="tip">通俗解释内容，2-3句话。</span></span>
```

```css
.term-box {
  position: relative;
  color: var(--primary-light);
  border-bottom: 1px dashed var(--primary-light);
  cursor: help;
  font-weight: 600;
}
.tip {
  display: none;
  position: absolute;
  bottom: calc(100% + 8px);
  left: 50%;
  transform: translateX(-50%);
  background: #1a1a2e;
  border: 1px solid var(--primary);
  border-radius: 8px;
  padding: 0.8rem 1rem;
  width: max-content;
  max-width: 320px;
  z-index: 100;
  font-size: 0.85rem;
  line-height: 1.6;
  color: var(--text);
  box-shadow: 0 4px 20px rgba(0,0,0,0.4);
}
.term-box:hover .tip { display: block; }
```

### 4. Analogy 通俗理解区块

每个核心概念配一个生活类比，带 emoji + 醒目边框。

```html
<div class="analogy">
  想象你在读一本小说，RNN 就像一个"逐字阅读者"...（生活化的类比描述）
</div>
```

```css
.analogy {
  background: linear-gradient(135deg, rgba(79,70,229,0.15), rgba(245,158,11,0.1));
  border-left: 4px solid var(--accent);
  border-radius: var(--radius);
  padding: 1.2rem 1.5rem;
  margin: 1.2rem 0;
  color: var(--text);
  line-height: 1.8;
  font-size: 0.95rem;
}
```

### 5. Side-Nav 侧边导航

固定在右侧的圆点导航，点击跳转 + 滚动自动高亮。

```css
.side-nav {
  position: fixed;
  right: 1.5rem;
  top: 50%;
  transform: translateY(-50%);
  display: flex;
  flex-direction: column;
  gap: 0.8rem;
  z-index: 100;
}
.side-nav a {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: rgba(255,255,255,0.2);
  display: block;
  transition: all 0.3s;
}
.side-nav a:hover, .side-nav a.active {
  background: var(--primary-light);
  transform: scale(1.4);
}
```

JS 逻辑（滚动高亮）：
```javascript
const sections = document.querySelectorAll('.section');
const navLinks = document.querySelectorAll('.side-nav a');
const observer = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (e.isIntersecting) {
      navLinks.forEach(l => l.classList.remove('active'));
      navLinks.forEach(l => { if (l.getAttribute('href') === '#' + e.target.id) l.classList.add('active'); });
    }
  });
}, { threshold: 0.3 });
sections.forEach(s => observer.observe(s));
```

### 6. Fade-In 滚动动画

所有 section 默认隐藏，进入视口时淡入上滑。

```css
.fade-in {
  opacity: 0;
  transform: translateY(30px);
  transition: opacity 0.6s, transform 0.6s;
}
.fade-in.visible {
  opacity: 1;
  transform: translateY(0);
}
```

```javascript
const fadeObserver = new IntersectionObserver((entries) => {
  entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('visible'); });
}, { threshold: 0.1 });
document.querySelectorAll('.fade-in').forEach(el => fadeObserver.observe(el));
```

### 7. Section Title

统一风格的章节标题，带编号圆点。

```css
.section-title {
  font-size: clamp(1.3rem, 3vw, 1.8rem);
  color: #fff;
  margin-bottom: 1.5rem;
  padding-bottom: 0.8rem;
  border-bottom: 2px solid var(--primary);
}
.section-title .num {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 2rem;
  height: 2rem;
  background: var(--primary);
  border-radius: 50%;
  font-size: 0.9rem;
  margin-right: 0.8rem;
  color: #fff;
}
```

## 交互组件（按需选用）

### Canvas 可视化

适用于：波形图、曲线图、分布图、动态过程演示。

```javascript
// 基础设置
const canvas = document.getElementById('waveCanvas');
const ctx = canvas.getContext('2d');
canvas.width = canvas.parentElement.clientWidth;
canvas.height = 200;
// 绑定 resize
window.addEventListener('resize', () => {
  canvas.width = canvas.parentElement.clientWidth;
  drawWave(); // 重绘函数
});
```

### Tab 切换

适用于：对比展示、多维度数据、消融实验。

```html
<div class="tab-bar">
  <button class="tab-btn active" data-tab="tab1">Tab 1</button>
  <button class="tab-btn" data-tab="tab2">Tab 2</button>
</div>
<div class="tab-content active" id="tab1">内容 1</div>
<div class="tab-content" id="tab2">内容 2</div>
```

```css
.tab-btn {
  background: transparent;
  color: var(--text-dim);
  border: 1px solid var(--bg-card2);
  padding: 0.6rem 1.2rem;
  border-radius: 8px 8px 0 0;
  cursor: pointer;
  font-size: 0.9rem;
  transition: all 0.3s;
}
.tab-btn.active { color: #fff; background: var(--bg-card); border-color: var(--primary); }
```

### SVG 架构图

适用于：系统架构、流程图、组件关系图。
使用内联 SVG，关键元素可绑定 click 事件展示详情。

### 点击交互

适用于：注意力权重查看、多头详情展开、层级信息。

```html
<div class="interactive-item" onclick="showDetail('xxx')">
  可点击元素
</div>
<div class="detail-panel" id="detail-xxx" style="display:none">
  展开详情
</div>
```

## 响应式设计

```css
@media (max-width: 700px) {
  .side-nav { right: 0.5rem; gap: 0.5rem; }
  .side-nav a { width: 8px; height: 8px; }
  .container { padding: 0 1rem; }
  .card { padding: 1rem; }
  .analogy { padding: 1rem; font-size: 0.9rem; }
  .tip { max-width: 250px; font-size: 0.8rem; }
}
```

## 字体建议

参考 frontend-slides 的字体选择理念，避免 Inter/Roboto 等泛用字体。推荐：
- 标题：Space Grotesk / DM Sans / Outfit
- 正文：Noto Sans SC（中文）/ Source Sans 3

```html
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;600;700&family=Noto+Sans+SC:wght@400;500;700&display=swap" rel="stylesheet">
```

## 内容编写规范

- 每个技术概念配一个 analogy 通俗理解
- 使用 emoji 标注要点（但不过度使用）
- 关键数字用 `<strong>` 或色块高亮
- 对比内容用表格或左右分栏
- 避免大段纯文字，用卡片 + 列表 + 图表 分散信息密度
- 每个术语首次出现时使用 term-box 悬浮解释