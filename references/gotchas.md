# 常见陷阱（Gotchas）

> **必须读完后才能开始生成 HTML。** 每一个陷阱都来自实际踩坑经验。

---

## 1. 导航脚本必须内联，禁止引用外部 JS 文件

**严重程度：致命（整个导航系统完全失效）**

`assets/nav-inject.js` 仅供参考学习其设计思路，**绝对不要**在实际生成的 HTML 中通过 `<script src="nav-inject.js">` 引用。

### 为什么？

1. **`file://` 协议安全限制**：用户在本地打开 HTML 文件时使用 `file://` 协议，浏览器可能阻止加载同目录的外部 JS 文件（取决于浏览器和用户设置）。一旦加载失败，整个导航系统（底部浮动栏、键盘快捷键、触摸滑动）全部消失。

2. **`SLIDE_ORDER` 文件名不匹配**：`nav-inject.js` 内部硬编码了原作者的文件名列表（`training-data.html`、`1-2-vocab.html` 等）。即使文件加载成功，`findIndex` 也找不到当前页面，导致 `idx = -1`，导航条不注入。

3. **`const` 重复声明冲突**：如果试图在 HTML 中用 `const SLIDE_ORDER = [...]` 覆盖 JS 文件中的同名变量，浏览器会抛出 `SyntaxError: Identifier 'SLIDE_ORDER' has already been declared`，导致后续所有代码不执行。

### 正确做法

将导航逻辑**完全内联**到每个 HTML 文件的 `</body>` 前。具体要求：

1. 每个页面包含相同的 `const SLIDE_ORDER = [...]` 声明，文件名必须与实际生成的文件一一对应
2. 紧跟一个 IIFE，包含：`cur`/`idx` 计算 → 底部导航条 DOM 创建 → 样式注入 → 键盘事件 → 触摸滑动
3. 中文字符（← → ☰ 等）直接使用 UTF-8 字面量，不要用 `\uXXXX` 转义（在字符串拼接中转义层级容易出错）
4. 用 `<!-- INLINE_NAV_START -->` 和 `<!-- INLINE_NAV_END -->` 注释标记包裹导航代码，便于后续批量替换

### 模板参考

```html
<!-- INLINE_NAV_START -->
<script>
const SLIDE_ORDER = [
  {file:'index.html', title:'目录'},
  {file:'s01-background.html', title:'论文背景'},
  // ... 与实际文件名完全一致
];
(function(){
  var cur = location.pathname.split('/').pop();
  var idx = SLIDE_ORDER.findIndex(function(s){ return s.file === cur; });
  if(idx < 0) return;
  // ... 底部导航条创建、样式注入、键盘/触摸事件
})();
</script>
<!-- INLINE_NAV_END -->
```

### 生成后验证

用 Node.js 对每个 HTML 做语法检查：
```
node --check extracted_inline_script.js
```

---

## 2. 文字墙（Text Wall）

**严重程度：高**

每页 50% 以上区域是纯文字，缺乏卡片、图示、动画等视觉元素。左栏文字占比超过 60%，右栏没有交互内容。

**规则**：每个内容块最多 2-3 句，绝不超 4 行。列表→卡片，步骤→流程图。

---

## 3. 缺失交互（No Interaction）

**严重程度：高**

页面只有静态文字和图片，用户无法点击、切换、推进步骤。右栏是空白或纯展示性内容。

**规则**：每个页面（除目录页外）的右栏必须有可交互元素——Tab 切换、步骤推进、场景选择、节点点击等。

---

## 4. Analogy 滥用或缺失

**严重程度：中**

- **缺失**：核心机制页面没有生活化类比，非技术读者无法理解。
- **滥用**：每段都加类比，或类比过于牵强（"就像机器学习一样"）。

**规则**：每个核心机制页面恰好 1 个 analogy，用日常生活场景（读书、购物、导航、做饭），先描述 2-3 句再映射回技术。

---

## 5. 暗色主题混用

**严重程度：中**

`design-system.md` 定义了暖白纸质感（`--bg: #f7f5f0`），但部分页面混用了 `design-spec.md` 的暗色主题变量（`--bg: #0f172a`）。

**规则**：统一使用 design-system.md 的 CSS 变量集，不要与 design-spec.md 混用。

---

## 6. 外部字体依赖

**严重程度：低**

引用 Google Fonts 等 CDN 字体，在离线或网络受限环境下页面文字显示异常。

**规则**：使用系统字体栈（`-apple-system, BlinkMacSystemFont, "PingFang SC", sans-serif`），不引入外部字体。

---

## 7. 响应式适配缺失

**严重程度：低**

页面只考虑了桌面横屏，没有竖屏提示、横屏自适应、小屏导航适配。

**规则**：必须包含横屏 16:9 自适应（`min(94vw, calc((100vh - 96px) * 16 / 9))`）和竖屏提示遮罩。

---

## 8. JS 中禁止使用 `\uXXXX` Unicode 转义表示中文

**严重程度：高（中文内容显示为乱码）**

在内联 JS 代码中使用 `\uXXXX` 转义序列表示中文字符（如写 `"\u505a\u68a6"` 代替 `"做梦"`），在某些编辑器和文件查看器中会直接显示转义码原文，而非解码后的汉字，导致用户看到乱码。

### 为什么？

1. **Python 字符串转义与 JS 转义混淆**：AI 在生成 Python 字符串时写入 `\uXXXX`，Python 的 `write()` 会原样输出这些字符。浏览器执行 JS 时能正确解析 `\uXXXX`，但用户用文本编辑器打开 HTML 源码时看到的是转义码而非汉字。
2. **量大难修**：一次生成可能产生数百甚至上千处 `\uXXXX`（实际案例：9 个文件共 1,716 处），逐一手动修复不现实。
3. **影响可读性和维护性**：即使浏览器渲染正常，源码中的转义码也让人类无法阅读和调试。

### 正确做法

- **所有中文内容一律使用 UTF-8 原生字符**：直接写 `"做梦"` 而非 `"\u505a\u68a6"`
- **包括但不限于**：导航按钮文本、气泡消息内容、提示信息、状态文字、步骤描述、章节标题
- **允许使用转义的场景**：仅限 JS 语法必需的转义（如 `\"`、`\\`、`\n`），不涉及中文

### 生成后自动化验证

生成所有 HTML 后，**必须**运行以下 Python 脚本扫描，确保零残留：

```python
import re, os

output_dir = "你的交付目录"  # 替换为实际路径
pattern = re.compile(r'\\u([0-9a-fA-F]{4})')

for fname in sorted(os.listdir(output_dir)):
    if not fname.endswith('.html'):
        continue
    with open(os.path.join(output_dir, fname), 'r', encoding='utf-8') as f:
        content = f.read()
    matches = pattern.findall(content)
    if matches:
        print(f"WARNING: {fname} 有 {len(matches)} 处残留")
    else:
        print(f"OK: {fname}")
```

---


## 质量检查清单

生成所有 HTML 后，逐项检查：

- [ ] **导航脚本内联**：没有 `<script src="nav-inject.js">`，导航逻辑完全内联
- [ ] **SLIDE_ORDER 文件名一致**：数组中的 `file` 值与实际生成的文件名完全匹配
- [ ] **JS 语法正确**：用 `node --check` 验证内联脚本无语法错误
- [ ] **每页 16:9 比例**：使用 `aspect-ratio: 16/9` 的 `.slide` 容器
- [ ] **CSS 变量统一**：使用 `--bg: #f7f5f0` 暖白主题
- [ ] **左讲解 + 右交互**：双栏布局，右栏有可交互元素
- [ ] **每个核心机制有 Analogy**：生活化类比，amber 色左边框
- [ ] **中文字符用 UTF-8 字面量**：整个 HTML 文件（含 JS、CSS、HTML）中不使用 `\uXXXX` 转义表示中文。**生成后必须用脚本扫描验证，确保零残留**
- [ ] **响应式适配**：横屏 + 竖屏处理
- [ ] **prefers-reduced-motion**：尊重用户的减少动画偏好