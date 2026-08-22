# 《去 AI 味 · 简洁优雅设计规范》v1.1

> md-editor / index.html 重构唯一视觉依据。制定：design-director。
> 执行：ui-engineer ｜ 验收：qa-reviewer（§8 为逐项打勾清单）。

## §0 北极星原则

**内容为主角，界面退后。** 三条判据，任何装饰不满足即砍掉：

1. 装饰不得损害文本对比度与可读性；
2. 全站只允许 **1 个强调色、1 种阴影、2 档圆角**；
3. 材质诚实——只用平面色块与 1px 发丝线，不伪装玻璃、不发光、不悬浮。

## §1 主题决策（v1.1 已反转）

**单主题 · 石墨深色。不跟随 prefers-color-scheme。**

理由：
- 用户明确选择深色主题；
- 深色不等于 AI 味——AI 味来自紫蓝渐变/辉光/极光这些装饰手法，而非深色本身；
- 石墨中性色系 + 唯一雾蓝强调色 + 发丝线分隔，同样满足"简洁大方美观优雅"；
- 导出 HTML 模板同步深色，保持编辑器 ↔ 导出成品视觉一致。

若未来需要浅色，按附录 A 整体替换令牌即可，规则不变。

## §2 色彩令牌（精确值，直接照抄）

```css
:root {
  /* 中性层 · 石墨深色 */
  --bg-base: #17181A;        /* 应用底色 · 石墨黑 */
  --bg-surface: #222428;     /* 弹层 / 表头 / 行内代码底 */
  --bg-hover: #2A2D31;       /* 悬停态 */
  --bg-active: #33373C;      /* 按下态 */
  --border: #2E3237;         /* 发丝线 */
  --border-strong: #3A3F45;

  /* 文本三级 */
  --text-primary: #E8E6E1;   /* 正文纸色，对石墨底 ≈14:1 */
  --text-secondary: #A6A29A; /* 辅助文字 ≈6.5:1 */
  --text-muted: #7B776E;     /* 仅占位符等非正文 */

  /* 唯一强调色 · 雾蓝 */
  --accent: #8CA6C4;         /* ≈7:1，链接安全 */
  --accent-hover: #A5BCD4;
  --accent-subtle: rgba(140, 166, 196, 0.10);
  --ring: rgba(140, 166, 196, 0.35);   /* 键盘焦点环 */

  /* 唯一阴影 · 仅弹层使用（深色底加重） */
  --shadow-popover: 0 12px 32px rgba(0, 0, 0, 0.45),
                    0 2px 8px rgba(0, 0, 0, 0.30);
}
```

提示框语义色板（功能性用色，不算第二强调色；深色下整体提亮保证可读性）：

| 类型 | 边线/底色基调 | 标题文字 |
| --- | --- | --- |
| note | `#6E96C4` @10% 底 | `#93B4DC` |
| tip | `#63B58C` @10% 底 | `#86C9A6` |
| important | `#A78BD6` @10% 底 | `#BBA5E4` |
| warning | `#D2A155` @10% 底 | `#E0B67A` |
| caution | `#D9766C` @10% 底 | `#E58F86` |

**废弃令牌**：`--accent-1/2/3`（紫/蓝/粉三色系）、一切 `*gradient*`、`--shadow-glow`、`--shadow-card`、半透明 `--bg-elevated`/旧 `--bg-surface`、`--radius-lg`(16px)。

## §3 字体方案

删除 `<link href="https://fonts.googleapis.com/...">` 整行（大陆访问不稳定且阻塞渲染），全站改系统字体栈：

```css
--font-ui: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
           "Helvetica Neue", Arial, "PingFang SC", "Hiragino Sans GB",
           "Microsoft YaHei", sans-serif;
--font-mono: ui-monospace, "SF Mono", "Cascadia Code", Consolas,
             Menlo, Monaco, "Courier New", monospace;
```

body / 按钮 / 标签用 `--font-ui`；textarea 与代码用 `--font-mono`。

字号与行高：

| 区域 | 字号 / 行高 |
| --- | --- |
| 编辑器 textarea | **15px / 1.8**（移动端必须 16px，防 iOS 聚焦自动放大） |
| 预览正文 | **16px / 1.85** |
| 行内代码 / 代码块 | 13.5px（行内 0.85em）/ 1.7 |
| 工具栏按钮文字 | 13px / 500–600 |
| 面板标签 / footer | 12px |

标题字距一律为 0（中文不吃负字距，删掉现有 `-0.02em/-0.01em`）。

## §4 布局 · 间距 · 圆角

- 高度：header **56px**（原64）｜ toolbar min-height **48px**、padding 8px×12px ｜ 面板标签条 **34px** ｜ footer **40px**（原44）
- 间距取 4px 栅格：4 / 8 / 12 / 16 / 24 / 32。编辑区 padding 24px（移动 16）；预览 padding 28×36px（移动 16×18）。
- 圆角收敛两档：`--radius-sm: 6px`（按钮、输入框、菜单条目、summary）、`--radius-md: 8px`（卡片、代码块、弹层、图片、logo 方块）；行内代码固定 4px 不入令牌；16px 档废除。
- 分隔策略：全部 1px `var(--border)` 发丝线；主分栏中缝改为 1px 线（不再用 gap+背景色模拟）。工具栏分组去掉带底色的盒子，组间放 20px 高竖直分隔线（左右各留 8px）。
- 面板标签：「编辑」「预览」两侧对称纯文字 12px `--text-secondary`——无大写、无字距、无圆点。建议编辑侧补同款标签（唯一允许的 DOM 新增，可选）。

## §5 组件规范

- **Logo**：28×28、radius-md 实心纸色方块（bg = `--text-primary`）+ 石墨色描边文档图标；标题「Markdown 编辑器」16px/600 纯色。零渐变、零光晕。
- **按钮只有两型**：
  - 主（导出）：反白实心 `--text-primary` 底 + 石墨字，hover 提亮 `#F2F0EB`；
  - 次（导入）：透明底 + 1px `--border-strong`，hover `--bg-hover`。
  - 统一 padding 7×14、sm 圆角、13px/500。无位移、无彩色投影。
- **工具栏按钮**：32×32（触屏 ≥40）、图标描边宽度 2、默认 `--text-secondary`，hover 变 primary + `--bg-hover`；active 仅背景加深至 `--bg-active`。禁止 scale。
- **弹层四兄弟**（导出菜单 / emoji 选择器 / 提示框集合 / tooltip）：`--bg-surface` 实底 + 1px `--border-strong` + `--shadow-popover`、md 圆角、内部条目 sm 圆角、hover `--bg-hover`；入场动画只保留 120ms opacity 淡入。backdrop-filter 全部清零。（深色下弹层边线用 strong 档保证与底面分离）
- **输入框**（emoji 搜索）：`--bg-base` 底（比弹层更深一档形成内嵌感）+ 1px `--border-strong`，focus 时边框转 `--accent` + 3px `--ring` 外圈。
- 滚动条 thumb `--border-strong`、hover `#4A5057`、宽 8px。
- `::selection { background: rgba(140,166,196,0.25); }`；caret-color `--accent`；`:focus-visible { outline: 2px solid var(--ring); outline-offset: 2px; }`
- `<meta name="theme-color" content="#17181A">` 移动端浏览器栏同色。

## §6 预览区排版（优雅感的核心载体）

- **标题全部纯色**（禁止 background-clip 渐变字）：h1 24px/700 上28 下14 + 底部 1px `--border`；h2 20px/600 上24 下12 + 底线；h3 17px/600 上20 下10；h4–h6 16px/600 色 `--text-secondary`。
- **引用块（普通）**：左 3px `var(--border-strong)` 实线、padding-left 14px、字色 secondary、无背景色。
- **提示框 admonition（五类全覆盖）**：左 3px 语义色实线 + 语义色 7% 透明度背景 + md 圆角 + padding 12×16；标题 13px/600 同语义色；正文正常墨色。无图标、无 blur、无阴影。⚠️ 现状缺 `-important` 与 `-caution` 两类样式（且残留无人使用的 `-danger`），本次五类必须全覆盖。
- **行内代码**：`--bg-surface` 底、无边框（删除紫色底/紫字/紫边）、4px 圆角、padding 1×5、0.85em、颜色随正文。
- **代码块**：`--bg-surface` 底 + 1px `--border` + md 圆角、padding 14×16、13.5px/1.7。
- **表格**：内外线均 1px `--border`；th `--bg-surface`/600；td padding 8×12；偶数行斑马纹 rgba(255,255,255,0.03)（可选）；md 圆角 overflow hidden。
- **折叠块 details**：1px `--border` 卡片、md 圆角；summary padding 10×14、hover `--bg-hover`；开合标记静态字符切换（收起 ▸ / 展开 ▾），禁旋转动画；内容区 padding 12×14。⚠️ 现有选择器写的是无效的 `details-content` 自定义元素，而 JS 实际生成 `div.details-content`，请改为 `.preview .details-content`（顺带修掉这个老 bug）。
- 图片：max-width 100%、md 圆角 + 1px `--border`。
- 列表 marker 统一 `--text-muted`（废除紫/蓝双色 marker）。hr 为 1px `--border`、上下 margin 28px。
- 链接：`--accent` 常显下划线（decoration-color rgba(140,166,196,.4)、offset 3px），hover 变实线。

## §7 微交互

仅允许 color / background-color / border-color / opacity / fill 参与过渡，统一 **120ms ease-out**。
禁止一切 transform 过渡与关键帧（translateY 上浮、scale 缩放、rotate、滑入）及 blur 过渡。
触摸反馈沿用现有 opacity 0.7 方案（合规保留）。

## §8 禁止清单（QA 验收逐项对照）

1. 渐变文字（任何 background-clip: text）
2. 彩色/发光阴影（含 logo 辉光、按钮 hover 光晕、panel 圆点光）
3. `--shadow-popover` 以外的一切阴影
4. 极光径向渐变背景（body::before 必须整段删除）
5. 网格纹理（body::after 必须整段删除）
6. 一切 backdrop-filter / -webkit-backdrop-filter（毛玻璃）
7. 多强调色并存（紫+蓝+粉三色系归一）
8. 半透明叠层表面（rgba 表面透底纹的做法）
9. hover/active 位移动画（translateY / scale / rotate）
10. 全大写 + 大字距面板标签
11. 渐变装饰圆点
12. 弹层滑入位移动画

## §9 实施要点（给 ui-engineer）

1. **JS 动态样式三处同步**：admonitionBtn 的菜单是 cssText 写死的深色毛玻璃（rgba(15,15,25,.95) + blur(20px)），必须重写为 §5 弹层规范；tooltip 在 CSS ::after 中、emoji popup 走类名——这两处改 CSS 即可。
2. 删字体 `<link>` 后，body / .editor / pre 三处 font-family 全部换栈，别漏。
3. id / class / data-action / 事件逻辑一律不动；唯一 DOM 变更 = 可选的编辑侧对称标签。
4. 导出 HTML 模板与主界面同步深色石墨令牌，保持编辑 ↔ 导出一致。
5. 完工自查：§8 清单逐项 + 五类提示框 + 移动端两档断点（768/480 结构不动，数值微调：header 52/48，编辑器字号恒 16px）。
6. README「主题设计」段落与新令牌强相关，属文档同步，另立任务处理，不阻塞本次改造。

## 附录 A（备而不用）：暖纸浅色备选案

v1.1 起浅色退居备选。仅当需求反转时整体替换令牌，禁止与主案混搭：
base `#FAF9F6` / surface `#F3F1EC` / hover `#ECEAE3` / active `#E4E1DA` / border `#E4E1DA` / border-strong `#D3CFC5` / text `#26251F`,`#6B685E`,`#98948A` / accent 黛蓝 `#365A82` / 主按钮墨底（`#26251F` 底 + 纸色字）/ 弹层 `#FFF` 实底 + border 普通档 / admonition 语义色 `note #3B6EA5`、`tip #2E7D57`、`important #7A5AA8`、`warning #9C6B1F`、`caution #B4443C` @7% 底。其余规则与本规范一致。

## 变更记录

- **v1.1**：用户选择深色主题，附录 B 石墨深色方案转正为 §2 主令牌；弹层边线升为 strong 档、阴影加重、admonition 语义色整体提亮（深色可读性）、emoji 输入框改 bg-base 内嵌感、新增 theme-color meta、导出模板同步深色。暖纸浅色移入附录 A 备用。
- **v1.0**：初版，单主题暖纸浅色定稿。
