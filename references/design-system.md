# 设计系统规范

基于洛小山 xueai.app / learn-ai 课程设计体系提炼。

## 目录

1. [设计原则](#1-设计原则)
2. [全局样式](#2-全局样式)
3. [色彩系统](#3-色彩系统)
4. [排版系统](#4-排版系统)
5. [布局模式](#5-布局模式)
6. [组件库](#6-组件库)
7. [动画系统](#7-动画系统)
8. [响应式与适配](#8-响应式与适配)
9. [交互模式](#9-交互模式)

---

## 1. 设计原则

- **暖白纸质感**：像在优质纸张上阅读，不是冰冷的深色终端
- **左讲解 + 右交互**：左侧放文字说明和步骤指引，右侧放可交互的可视化
- **步骤式渐进披露**：不一次性展示所有内容，通过"下一步"按钮逐步揭示
- **语义色板**：每种颜色有明确语义（蓝=强调/主色、amber=注意、绿=正确/成功、红=危险/错误、紫=特殊）
- **零依赖**：单文件 HTML，所有 CSS/JS 内联，不依赖任何外部框架

## 2. 全局样式

### Reset

```css
* { box-sizing: border-box; margin: 0; padding: 0; }
body {
  background: #e5e2da;
  font-family: -apple-system, BlinkMacSystemFont, "PingFang SC", sans-serif;
  height: 100vh; overflow: hidden;
  display: flex; align-items: center; justify-content: center;
  -webkit-font-smoothing: antialiased;
}
```

### 页面淡入

```css
@keyframes _si { from { opacity: 0 } to { opacity: 1 } }
body { opacity: 0; animation: _si 0.15s ease 0.08s forwards; }
```

### 幻灯片容器

```css
.slide {
  width: 94vw; max-width: 1440px;
  aspect-ratio: 16 / 9;
  background: var(--bg);
  border-radius: 16px;
  box-shadow: 0 20px 60px rgba(0,0,0,0.08);
  display: flex; flex-direction: column;
  overflow: hidden; position: relative;
}
```

## 3. 色彩系统

### CSS 变量（统一使用这套变量名）

```css
:root {
  --bg: #f7f5f0;           /* 幻灯片背景（暖白纸质感） */
  --text: #1c1c1e;         /* 主文字 */
  --sub: #6b7280;          /* 辅助文字 */
  --muted: #8e8e93;        /* 更弱的文字 */
  --border: rgba(0,0,0,0.07); /* 边框 */
  --surface: white;        /* 卡片/面板背景 */
  --surface2: #f0ede6;     /* 次级表面 */
  --accent: #0066ff;       /* 主强调色（蓝） */
  --amber: #d97706;        /* 注意/警示 */
  --success: #16a34a;      /* 成功/正确 */
  --danger: #dc2626;       /* 危险/错误 */
  --purple: #7c3aed;       /* 特殊/紫色 */
  --orange: #ea580c;       /* 橙色强调 */
  --cyan: #0891b2;         /* 青色 */
  --gold: #b07d20;         /* 金色（哲学/洞见） */
}
```

### 语义色使用规则

| 颜色 | 使用场景 | CSS 用法 |
|------|---------|---------|
| accent 蓝 | 主按钮、链接、高亮、当前选中 | `background: var(--accent)` |
| amber | 注意事项、决策提示、需要警惕的内容 | `border-color: rgba(217,119,6,0.25)` |
| success 绿 | 正确做法、推荐方案、正面结果 | `background: rgba(22,163,74,0.05)` |
| danger 红 | 错误示范、风险警告、幻觉标记 | `color: var(--danger)` |
| purple | 特殊概念、高级功能、第三维度 | `background: rgba(124,58,237,0.1)` |
| gold | 哲学思考、核心洞见、重要结论 | `color: var(--gold)` |

### 边框与背景的层次感

```css
/* 卡片：白色底 + 微弱边框 */
.card {
  background: var(--surface);
  border-radius: 12px;
  border: 1.5px solid var(--border);
}

/* 语义卡片：带颜色倾向 */
.card-blue { background: rgba(0,102,255,0.04); border-color: rgba(0,102,255,0.15); }
.card-green { background: rgba(22,163,74,0.05); border-color: rgba(22,163,74,0.2); }
.card-red { background: rgba(220,38,38,0.04); border-color: rgba(220,38,38,0.18); }
.card-amber { background: rgba(217,119,6,0.06); border-color: rgba(217,119,6,0.2); }
```

## 4. 排版系统

### 字号层级

| 用途 | 字号 | 字重 | 行高 | 示例 |
|------|------|------|------|------|
| 页面主标题 | 40-52px | 900 | 1.15 | `.main-title` |
| Section 标题 | 22-26px | 800 | 1.3 | `.hd-title` |
| 卡片标题 | 15-18px | 700 | 1.3 | `.scene-name` |
| 正文 | 14-15px | 400 | 1.7 | `.desc-text` |
| 辅助文字 | 12-13px | 600 | 1.6 | `.step-desc` |
| 标签/徽章 | 10-12px | 700-800 | 1 | `.badge`, `.pill` |
| eyebrow | 11-13px | 800 | 1 | `.eyebrow`（大写字母间距） |

### 字体使用

- **系统字体栈**，不引入外部字体（保持零依赖和加载速度）
- `font-family: -apple-system, BlinkMacSystemFont, "PingFang SC", sans-serif`
- 代码字体：`font-family: 'Cascadia Code', 'Fira Code', 'SF Mono', monospace`

### 文字着色规则

- 主文字 `color: var(--text)` = `#1c1c1e`
- 强调文字使用语义色：`color: var(--accent)` 而非 `<strong>` 标签
- 辅助说明 `color: var(--sub)`
- 极弱文字 `color: var(--muted)`

## 5. 布局模式

### 5.1 标准双栏（左讲解 + 右交互）

**最高频布局，48/69 页使用。**

```css
.body { flex: 1; display: flex; min-height: 0; }
.left {
  width: 40%; padding: 20px 28px;
  border-right: 1px solid var(--border);
  display: flex; flex-direction: column; gap: 14px;
  overflow-y: auto;
}
.right {
  flex: 1; padding: 20px 28px;
  display: flex; flex-direction: column; gap: 12px;
  overflow: hidden;
}
```

左栏放：标题、描述、步骤列表、洞察卡片
右栏放：交互演示、图表、动画、可视化

### 5.2 三栏（左导航 + 中内容 + 右面板）

```css
.body { display: grid; grid-template-columns: 260px 1fr; }
.nav-col { border-right: 1px solid var(--border); overflow-y: auto; }
.demo-col { padding: 24px 30px; }
```

### 5.3 均分双栏（VS 对比）

```css
.body { display: grid; grid-template-columns: 1fr 1fr; }
```

### 5.4 单栏居中（总结/哲学页）

```css
.body { padding: 28px 40px; display: flex; flex-direction: column; }
```

### Header

```css
.hd {
  padding: 16px 36px;
  display: flex; justify-content: space-between; align-items: center;
  border-bottom: 1px solid var(--border);
  flex-shrink: 0;
}
.hd-tag { font-size: 14px; font-weight: 800; color: var(--amber); }
.hd-title { font-size: 24px; font-weight: 800; color: var(--text); }
```

## 6. 组件库

### 6.1 Badge / Pill（标签）

```css
.badge {
  font-size: 12px; font-weight: 800; letter-spacing: 0.5px;
  padding: 5px 14px; border-radius: 8px; white-space: nowrap;
}
.badge-blue { background: rgba(0,102,255,0.08); color: var(--accent); }
.badge-green { background: rgba(22,163,74,0.1); color: var(--success); }
.badge-amber { background: rgba(217,119,6,0.1); color: var(--amber); }

.pill {
  font-size: 11px; font-weight: 600; padding: 3px 10px;
  border-radius: 20px; border: 1px solid var(--border);
  background: rgba(0,0,0,0.03); color: var(--sub);
}
```

### 6.2 按钮

```css
.btn-primary {
  background: var(--accent); color: white; border: none;
  padding: 10px 24px; border-radius: 12px;
  font-size: 14px; font-weight: 600; cursor: pointer;
  transition: all 0.2s;
}
.btn-primary:hover { filter: brightness(1.1); transform: translateY(-1px); }
.btn-primary:disabled { background: #ccc; cursor: not-allowed; transform: none; filter: none; }

.btn-reset {
  background: var(--surface2); color: var(--sub);
  border: 1px solid var(--border);
  padding: 8px 14px; border-radius: 10px;
  font-size: 13px; font-weight: 600; cursor: pointer;
}
```

### 6.3 场景卡片（可点击选择）

```css
.scene-card {
  background: white; border-radius: 12px; padding: 14px 18px;
  border: 2px solid transparent; cursor: pointer; transition: all 0.2s;
}
.scene-card:hover { border-color: rgba(22,163,74,0.25); }
.scene-card.active { border-color: var(--success); background: rgba(22,163,74,0.03); }
.scene-tag { font-size: 12px; font-weight: 800; color: var(--success); }
.scene-name { font-size: 16px; font-weight: 700; color: var(--text); }
.scene-desc { font-size: 13px; color: var(--sub); line-height: 1.5; }
```

### 6.4 Tab 切换

```css
.tab-group { display: flex; gap: 6px; }
.tab-btn {
  padding: 7px 18px; border-radius: 10px;
  border: 1px solid rgba(0,0,0,0.1);
  font-size: 13px; font-weight: 600; cursor: pointer;
  background: white; color: var(--sub); transition: all 0.15s;
}
.tab-btn.active { background: var(--text); color: white; border-color: var(--text); }

/* 带语义色的 tab（如算法对比） */
.algo-tab.active.cnn  { background: #fff7ed; color: #c2410c; border-color: #fdba74; }
.algo-tab.active.rnn  { background: #f0fdf4; color: #15803d; border-color: #86efac; }
.algo-tab.active.gpt  { background: #eff6ff; color: #1d4ed8; border-color: #93c5fd; }
```

### 6.5 VS 对比双栏

```css
.vs { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.vs-box { border-radius: 14px; padding: 22px 24px; }
.vs-box.do { background: rgba(22,163,74,0.05); border: 1.5px solid rgba(22,163,74,0.2); }
.vs-box.skip { background: rgba(220,38,38,0.04); border: 1.5px solid rgba(220,38,38,0.15); }
.vs-hd { font-size: 20px; font-weight: 800; }
.vs-box.do .vs-hd { color: var(--green); }
.vs-box.skip .vs-hd { color: var(--red); }
.vs-li { font-size: 17px; color: var(--sub); line-height: 1.8; padding-left: 18px; position: relative; }
.vs-li::before { content: '•'; position: absolute; left: 0; }
```

### 6.6 流程图节点

```css
.flow-node {
  flex: 1; background: white; border-radius: 12px; padding: 16px 18px;
  display: flex; flex-direction: column; gap: 6px; position: relative;
}
.flow-node.active-node { border: 2px solid var(--accent); }
.flow-node-num { font-size: 12px; font-weight: 800; color: var(--sub); }
.flow-node-title { font-size: 15px; font-weight: 700; color: var(--text); }
.flow-node-desc { font-size: 13px; color: var(--sub); line-height: 1.6; }
.flow-node-tag {
  display: inline-block; margin-top: 4px;
  font-size: 11px; font-weight: 700; padding: 3px 10px; border-radius: 4px;
  background: rgba(0,102,255,0.08); color: var(--accent);
}
.flow-arrow-v { width: 100%; text-align: center; color: var(--sub); font-size: 18px; }
.flow-arrow-h {
  width: 28px; display: flex; align-items: center; justify-content: center;
  color: var(--sub); font-size: 18px;
}
```

### 6.7 聊天气泡（对话/演示）

```css
.bubble-user {
  background: var(--accent); color: white;
  padding: 11px 15px; border-radius: 12px 12px 4px 12px;
  font-size: 15px; line-height: 1.6; align-self: flex-end; max-width: 90%;
}
.bubble-ai {
  background: rgba(0,0,0,0.04); color: var(--text);
  padding: 11px 15px; border-radius: 12px 12px 12px 4px;
  font-size: 15px; line-height: 1.8; align-self: flex-start; max-width: 95%;
}
.bubble-retrieval {
  background: rgba(217,119,6,0.06); border: 1px dashed rgba(217,119,6,0.3);
  padding: 10px 14px; border-radius: 10px;
  font-size: 13px; color: #92400e; line-height: 1.6;
}
```

### 6.8 Token 可视化

```css
.tk {
  padding: 7px 11px; border-radius: 8px;
  font-size: 14px; font-weight: 700;
  cursor: default; transition: all 0.2s; user-select: none;
}
.tk-default { background: rgba(0,0,0,0.05); color: var(--text); }
.tk-active  { background: #dbeafe; color: #1d4ed8; border: 1.5px solid #93c5fd; transform: scale(1.08); }
.tk-context { background: #f0ede6; color: var(--text); }
```

### 6.9 概率条

```css
.prob-bar-track { flex: 1; height: 22px; background: rgba(0,0,0,0.06); border-radius: 5px; overflow: hidden; }
.prob-bar-fill {
  height: 100%; border-radius: 5px;
  transition: width 0.55s cubic-bezier(0.25,1,0.5,1);
  min-width: 0;
}
.prob-bar-fill.top { background: var(--accent); }
.prob-bar-fill:not(.top) { background: var(--sub); }
```

### 6.10 进度步骤条

```css
.prog-item { display: flex; gap: 12px; align-items: flex-start; opacity: 0.3; transition: opacity 0.35s; }
.prog-item.done { opacity: 1; }
.prog-item.cur { opacity: 1; }
.prog-dot {
  width: 28px; height: 28px; border-radius: 50%;
  background: var(--surface2); border: 2px solid var(--border);
  display: flex; align-items: center; justify-content: center;
  font-size: 12px; font-weight: 800; color: var(--muted);
}
.prog-item.done .prog-dot { background: var(--green); border-color: var(--green); color: white; }
.prog-item.cur .prog-dot { background: var(--orange); border-color: var(--orange); color: white; }
.prog-line { width: 2px; flex: 1; min-height: 10px; background: var(--border); }
```

### 6.11 统计数字

```css
.stats { display: flex; gap: 10px; }
.stat { flex: 1; border-radius: 10px; padding: 11px 14px; text-align: center; }
.stat .sv { font-size: 24px; font-weight: 900; }
.stat .sl { font-size: 12px; font-weight: 700; margin-top: 2px; color: var(--muted); }
.stat.blue-s { background: rgba(0,102,255,0.07); border: 1px solid rgba(0,102,255,0.15); }
.stat.blue-s .sv { color: var(--accent); }
```

### 6.12 洞察/提示卡片

```css
.insight {
  background: rgba(217,119,6,0.06); border: 1.5px solid rgba(217,119,6,0.2);
  border-radius: 10px; padding: 10px 14px;
  font-size: 14px; color: var(--text); line-height: 1.6;
}
.tip {
  border-radius: 10px; padding: 11px 14px;
  font-size: 14px; line-height: 1.65;
}
.tip.green { background: rgba(22,163,74,0.06); border: 1px solid rgba(22,163,74,0.2); color: var(--green); }
.tip.amber { background: rgba(217,119,6,0.07); border: 1px solid rgba(217,119,6,0.22); color: var(--amber); }
.tip.blue { background: rgba(0,102,255,0.06); border: 1px solid rgba(0,102,255,0.2); color: var(--accent); }
```

### 6.13 宣言/金句卡片（哲学/洞见页专用）

```css
.mantra {
  background: linear-gradient(135deg, rgba(176,125,32,0.1) 0%, rgba(91,79,207,0.06) 100%);
  border: 2px solid rgba(176,125,32,0.25); border-radius: 18px;
  padding: 0 40px; text-align: center;
  display: flex; flex-direction: column; align-items: center; justify-content: center;
}
.mantra-main { font-size: 38px; font-weight: 900; color: var(--gold); line-height: 1.45; }
```

## 7. 动画系统

### 7.1 页面淡入

```css
@keyframes _si { from { opacity: 0 } to { opacity: 1 } }
body { opacity: 0; animation: _si 0.15s ease 0.08s forwards; }
```

### 7.2 元素渐显（步骤推进核心动画）

```css
.step-item {
  opacity: 0; transform: translateY(8px);
  transition: opacity 0.35s, transform 0.35s;
}
.step-item.show { opacity: 1; transform: translateY(0); }
```

### 7.3 消息气泡渐显

```css
.msg {
  opacity: 0; transform: translateY(10px);
  transition: opacity 0.35s ease, transform 0.35s ease;
}
.msg.show { opacity: 1; transform: translateY(0); }
```

### 7.4 从左滑入

```css
.step {
  opacity: 0; transform: translateX(-20px);
  transition: opacity 0.5s ease, transform 0.5s ease;
}
.step.show { opacity: 1; transform: translateX(0); }
```

### 7.5 从下滑入

```css
.rcard {
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.45s ease, transform 0.45s ease;
}
.rcard.show { opacity: 1; transform: translateY(0); }
```

### 7.6 弹性缩放

```css
@keyframes pop { 0% { transform: scale(0.95) } 60% { transform: scale(1.02) } 100% { transform: scale(1) } }
.pop { animation: pop 0.3s ease; }
```

### 7.7 抖动（错误反馈）

```css
@keyframes shake { 0%,100% { transform: translateX(0) } 20%,60% { transform: translateX(-4px) } 40%,80% { transform: translateX(4px) } }
.shake { animation: shake 0.4s ease; }
```

### 7.8 打字光标闪烁

```css
@keyframes blink { 0%,100% { opacity: 1 } 50% { opacity: 0 } }
.bubble-typing { display: inline-block; width: 6px; height: 14px; background: var(--sub); border-radius: 2px; margin-left: 2px; animation: blink 0.7s step-end infinite; }
```

### 动画使用规则

- 所有元素默认 `opacity: 0`，通过 JS 添加 `.show` 类触发渐显
- 过渡时长统一 0.35s，偏重的用 0.45s-0.5s
- 缓动函数：位移用 `ease`，进度条用 `cubic-bezier(0.25,1,0.5,1)`
- 支持 `prefers-reduced-motion`：减少动画时直接设置 `opacity: 1; transform: none`

## 8. 响应式与适配

### 8.1 横屏适配

```css
@media (orientation: landscape) {
  .slide {
    width: min(94vw, calc((100vh - 96px) * 16 / 9)) !important;
    max-width: 1440px !important;
  }
}
```

### 8.2 竖屏提示

小屏竖屏时显示"请横屏观看"遮罩，提供关闭按钮让用户仍可竖屏浏览。

### 8.3 竖屏 Canvas 缩放

```css
/* 竖屏时强制设定设计分辨率后整体缩小 */
@media (orientation: portrait) and (max-width: 1024px) {
  .slide {
    width: 960px !important; height: 540px !important;
    transform: scale(var(--portrait-scale)) !important;
    transform-origin: top left;
  }
}
```

### 8.4 移动端导航

底部导航条响应式：768px 以下缩小间距和字号，按钮文字截断。

## 9. 交互模式

### 9.1 步骤推进（Step-by-Step）

**最核心交互模式。** 左栏显示步骤列表，右栏根据当前步骤显示对应内容。

JS 模式：
```javascript
let step = 0;
function nextStep() {
  step++;
  // 更新左栏步骤高亮
  document.querySelectorAll('.prog-item').forEach((el, i) => {
    el.classList.toggle('done', i < step);
    el.classList.toggle('cur', i === step);
  });
  // 更新右栏内容
  document.querySelectorAll('.rcard').forEach((el, i) => {
    el.classList.toggle('active', i === step);
    if (i === step) setTimeout(() => el.classList.add('show'), 50);
  });
  // 禁用按钮
  document.getElementById('btnNext').disabled = (step >= maxSteps);
}
```

### 9.2 Tab 切换

```javascript
document.querySelectorAll('.tab-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    btn.parentElement.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.querySelectorAll('.algo-pane').forEach(p => p.classList.remove('active'));
    document.getElementById(btn.dataset.tab).classList.add('active');
  });
});
```

### 9.3 场景选择

左栏列出多个场景卡片，点击切换右栏展示内容。

```javascript
document.querySelectorAll('.scene-card').forEach(card => {
  card.addEventListener('click', () => {
    document.querySelectorAll('.scene-card').forEach(c => c.classList.remove('active'));
    card.classList.add('active');
    // 更新右栏
  });
});
```

### 9.4 键盘导航

- 单独 `→`：调用 `nextStep()` 推进当前页面步骤
- `Cmd+→`：跳转下一页幻灯片
- `Cmd+←`：跳转上一页幻灯片
- `Cmd+↑`：返回目录页

### 9.5 触摸滑动

底部热区检测左右滑动手势，超过 40px 阈值触发翻页。