# Diagram Design 中文使用说明书

> 本文依据 GitHub 仓库 [cathrynlavery/diagram-design](https://github.com/cathrynlavery/diagram-design) 的 `main` 分支整理，对应插件版本 **2.6.165**（仓库最后更新于 2026-11-18）。这是第三方整理的中文说明，并非上游官方文档；上游仍在快速迭代，安装后请以你本地版本和仓库 README 为准。

---

## 目录

1. [这个项目是什么](#1-这个项目是什么)
2. [它是怎么工作的](#2-它是怎么工作的)
3. [安装](#3-安装)
4. [快速开始](#4-快速开始)
5. [支持的 50 种图表](#5-支持的-50-种图表)
6. [品牌定制：让图长得像你的网站](#6-品牌定制让图长得像你的网站)
7. [导入已有图表](#7-导入已有图表)
8. [导出 SVG / PNG](#8-导出-svg--png)
9. [命令、配置与目录速查](#9-命令配置与目录速查)
10. [设计规则：它为什么好看](#10-设计规则它为什么好看)
11. [常见问题与限制](#11-常见问题与限制)
12. [参考链接](#12-参考链接)

---

## 1. 这个项目是什么

**Diagram Design 是一个“智能体技能”（Agent Skill），不是画图软件。** 你用自然语言把想画的内容告诉 AI 编程助手，它按一套固定的编辑设计规范，生成**一个自包含的 HTML 文件**（图表本身是内联 SVG）。

核心特征：

- **50 种图表类型**，每种都有浅色极简、深色极简、完整编辑风三种静态变体。
- **单文件交付**：一个 `.html`，双击即可在浏览器打开；内联 CSS 与 SVG，没有构建步骤，也不依赖外部图片。
- **可品牌化**：读取你的网站后，把配色和字体映射到设计 token，之后每张图都用你的品牌视觉。
- **可导入**：把 draw.io、Mermaid、Excalidraw、PlantUML 的旧图重画成编辑风格，而不是原样转换。
- **可导出**：需要交付给 Figma、幻灯片或社交媒体时，导出 SVG 或 PNG。
- **默认可访问**：SVG 带 `role="img"`、`aria-labelledby`、`<title>` / `<desc>`；支持减少动态效果偏好。

它最适合：博客文章、产品文档、演示文稿、方案汇报里**要正式发布**的示意图。

它不适合：

- 每天随代码变更、需要在 Git 里 diff 的图 —— 用 Mermaid。
- 纯文字列表、属性对比表、单个方框 —— 直接用表格或一句话。
- 想要一个可以拖拽节点的可视化编辑器 —— 它不是 draw.io / Figma。

作者是 **Cathryn Lavery**，项目以 MIT 许可证开源。官网：[diagramdesign.dev](https://diagramdesign.dev)；在线图库：[cathrynlavery.github.io/diagram-design](https://cathrynlavery.github.io/diagram-design/)。

---

## 2. 它是怎么工作的

### 2.1 渐进披露：只加载需要的内容

安装后，AI 助手在启动时通常只看到技能的名称和一句描述。只有当你提出画图需求时，它才会按需读取：

1. `SKILL.md`：设计哲学、类型选择、检查清单；
2. 与你需求匹配的**一个** `references/type-*.md`：该类型的布局语法与预算；
3. 需要表达“行为/状态/风险”时，再加 `references/semantic-patterns.md`；
4. 只有你明确要求动画时，才加 `references/animation.md`。

这套“渐进披露”让模型的上下文保持精简：无论项目支持多少种图，它一次只读当前需要的那一种。新增图表类型也不会拖慢其他类型。

### 2.2 生成流程

一次典型的画图过程是：

1. **选语义模式**（可选）：当图的重点在排队、瓶颈、策略判定、信任边界等“行为”上时，先选行为模式，再选视觉类型。
2. **选视觉类型**：架构图、流程图、时序图、四象限……
3. **先说明计划**：类型、尺寸预设、复杂度预算会裁掉什么。用户在场时可以先纠正，不在场时则把假设写在交付物旁边。
4. **加载对应类型参考**，在 4px 网格上排版。
5. **跑输出前检查**：节点是否可删、强调色是否不超过 2 个、连接线是否正交、SVG 是否可访问等。

### 2.3 输出形态

- 单个 `.html` 文件，CSS 内嵌，SVG 内联。
- 默认**完全静态、没有 JavaScript**；只有明确要求动画时才加入少量受控脚本。
- 默认从 Google Fonts 加载 Instrument Serif、Geist、Geist Mono —— 这是唯一的网络请求；切换为系统字体后可以做到**零网络请求**。
- 动画输出也必须“静态优先”：没有 JS、打印、减少动态效果时，仍展示完整的一帧。

---

## 3. 安装

项目同时提供通用 Agent Skills 安装方式和各宿主（Host）的原生插件方式。**官方构建只来自这个仓库**；LittleMight（作者的公司）负责插件目录的上架，其他名称下的列表属于非官方副本。

### 3.1 通用安装（Cursor、Cline、Gemini CLI、Windsurf、Amp 等）

```bash
npx skills add cathrynlavery/diagram-design
```

`skills` CLI 会识别仓库中的 `skills/diagram-design/SKILL.md`，把整个技能（`references/`、`assets/`、`scripts/`）安装到你选择的所有宿主根目录。

- 当选择多个技能目录时，CLI 推荐 **Symlink**：只保留一份源，链接到各宿主，更新一次即可全部生效。
- 想为每个宿主保留独立副本时加 `--copy`。
- 这是独立安装，**不会自动跟随插件市场更新**；后续用 `npx skills update diagram-design` 拉取更新。

### 3.2 Claude Code

```text
/plugin marketplace add cathrynlavery/diagram-design
/plugin install diagram-design@diagram-design
```

安装后可在 `/plugin` → **Marketplaces** → **diagram-design** → **Enable auto-update** 打开自动更新（Claude Code 默认对第三方市场关闭自动更新）。提示时运行 `/reload-plugins`，或让下一次会话加载更新。

### 3.3 Codex

```bash
codex plugin marketplace add cathrynlavery/diagram-design
codex plugin add diagram-design@diagram-design
```

Codex 会在启动时刷新已配置的 Git 市场；想立即拉取更新，运行 `codex plugin marketplace upgrade diagram-design` 后开新会话。

### 3.4 GitHub Copilot

```bash
copilot plugin marketplace add cathrynlavery/diagram-design
copilot plugin install diagram-design@diagram-design
```

用 `copilot skill list`（交互会话里用 `/skills`）确认技能被发现，然后直接用自然语言提出画图需求。更新：

```bash
copilot plugin marketplace update diagram-design
copilot plugin update diagram-design@diagram-design
```

### 3.5 Factory Droid

```bash
droid plugin marketplace add https://github.com/cathrynlavery/diagram-design
droid plugin install diagram-design@diagram-design --scope user
```

Droid 按提交而不是清单里的展示版本跟踪 Git 插件。更新：

```bash
droid plugin marketplace update diagram-design
droid plugin update diagram-design@diagram-design --scope user
```

### 3.6 Pi

```bash
pi install https://github.com/cathrynlavery/diagram-design
```

在已打开的 Pi 会话中运行 `/reload`。Pi 会把技能用于匹配的画图请求；也可以显式调用 `/skill:diagram-design`。Pi 没有自动包刷新，用 `pi update --extensions` 拉取更新。

### 3.7 Qoder

```bash
git clone https://github.com/cathrynlavery/diagram-design.git
qoder plugins validate ./diagram-design
qoder plugins install ./diagram-design
qoder plugins list
```

Qoder IDE 也可以用仓库内受控的 ZIP 通过 **Extensions → Plugins → Add Plugins → Upload Plugin** 导入：

```bash
git -C diagram-design archive --format=zip \
  --output=../diagram-design-qoder.zip HEAD \
  .qoder-plugin commands skills LICENSE
```

安装或更新后开一个新任务。

### 3.8 Kiro

导入这个子目录 URL：

```text
https://github.com/cathrynlavery/diagram-design/tree/main/skills/diagram-design
```

Kiro 会把技能复制到工作区的 `.kiro/skills/` 或全局的 `~/.kiro/skills/`；更新时需要重新导入。

### 3.9 OpenCode

把 `skills/diagram-design/` 复制或软链到项目的 `.opencode/skills/diagram-design`，或全局的 `~/.config/opencode/skills/diagram-design`。OpenCode 没有 Diagram Design 市场包，复制安装需要在更新时替换整个目录。

### 3.10 Claude Cowork（组织市场）

组织级 GitHub 市场目前要求私有或内部仓库。先把公开仓库镜像到组织拥有的仓库，在 **Organization settings → Plugins → Add plugin → GitHub** 连接镜像，并在市场菜单打开 **Sync automatically**。只有包含插件版本升级的 PR 合并到镜像默认分支时才会自动同步，直接 push 不会触发 webhook。

### 3.11 可编辑安装（想直接改风格指南）

如果打算直接修改 `references/style-guide.md`，请克隆仓库并安装本地路径；托管安装的更新可能会覆盖你对工作副本的修改。

```bash
git clone git@github.com:cathrynlavery/diagram-design.git ~/code/diagram-design

# Pi：注册本地包
pi install ~/code/diagram-design

# Claude Code：软链内部技能目录
ln -s ~/code/diagram-design/skills/diagram-design ~/.claude/skills/diagram-design
```

其他 Agent Skills 宿主可以只创建自己使用的根目录，再把内部技能目录软链过去：

```bash
mkdir -p ~/.agents/skills ~/.cursor/skills ~/.cline/skills \
  ~/.kiro/skills ~/.config/opencode/skills ~/.copilot/skills

ln -s ~/code/diagram-design/skills/diagram-design ~/.agents/skills/diagram-design
ln -s ~/code/diagram-design/skills/diagram-design ~/.cursor/skills/diagram-design
ln -s ~/code/diagram-design/skills/diagram-design ~/.cline/skills/diagram-design
ln -s ~/code/diagram-design/skills/diagram-design ~/.kiro/skills/diagram-design
ln -s ~/code/diagram-design/skills/diagram-design ~/.config/opencode/skills/diagram-design
ln -s ~/code/diagram-design/skills/diagram-design ~/.copilot/skills/diagram-design
```

Windows 请使用目录联接（junction），不要用文件软链：

```powershell
$src = "E:\diagram-design\skills\diagram-design"
foreach ($t in @(
  "$env:USERPROFILE\.claude\skills\diagram-design",
  "$env:USERPROFILE\.cursor\skills\diagram-design",
  "$env:USERPROFILE\.agents\skills\diagram-design",
  "$env:USERPROFILE\.cline\skills\diagram-design",
  "$env:USERPROFILE\.kiro\skills\diagram-design",
  "$env:USERPROFILE\.config\opencode\skills\diagram-design",
  "$env:USERPROFILE\.copilot\skills\diagram-design"
)) {
  $parent = Split-Path $t -Parent
  if (-not (Test-Path $parent)) { New-Item -ItemType Directory -Path $parent -Force | Out-Null }
  if (-not (Test-Path $t)) { cmd /c mklink /J "$t" "$src" }
}
```

> 同一个宿主如果既扫描自己的原生目录又扫描 `.agents/skills/`，只选其中一个，避免重复发现同一个技能。

---

## 4. 快速开始

### 4.1 直接对助手说

安装后，用平常的话描述内容和用途即可：

- “给我的应用画一张架构图：前端、后端、数据库、Redis 缓存。”
- “画一张 Q2 项目按影响和投入排布的四象限图。”
- “画一个 Bearer 调用在 401 时刷新令牌的时序图。”
- “把 diagram-design 的品牌配置到 `https://yoursite.com`。”
- “把这张 draw.io 图重画成适合放进演示文稿的版本。”

助手会自己选类型、先说明计划，然后写出一个可双击打开的 `.html` 文件。

### 4.2 首次使用：风格门槛

在**新项目**里第一次画图时，如果 `style-guide.md` 还是出厂默认值，技能不会悄悄用默认皮肤，而是暂停并询问：

> 这是本项目的第一次出图，风格指南仍是默认值。要现在定制吗？选项：
> (a) 提供网站 URL；(b) 从已安装技能提取；(c) 从本地文件夹/设计系统提取；(d) 手动粘贴 token；(e) 保持默认；(f) 加载已保存的 profile。

选择后会进入对应的 onboarding 流程。一旦风格被定制过，或你明确选择默认，之后就不再拦问。

### 4.3 先看图库，再动手

克隆仓库后可以直接打开图库，把 50 种图、三种变体都翻一遍：

```bash
# macOS
open skills/diagram-design/assets/index.html
# Linux
xdg-open skills/diagram-design/assets/index.html
# Windows
start skills/diagram-design/assets/index.html
```

也可以从模板开始：

```bash
cp skills/diagram-design/assets/template.html my-diagram.html        # 浅色极简
cp skills/diagram-design/assets/template-full.html my-diagram.html   # 完整编辑风（带摘要卡片）
cp skills/diagram-design/assets/template-motion.html my-diagram.html # 可选的无障碍动画
```

建议把生成的 HTML 写进**项目自己的目录**（例如 `docs/diagrams/`），不要在技能包的 `assets/` 里直接改示例。

### 4.4 自检

生成后可在仓库根目录运行：

```bash
python3 skills/diagram-design/scripts/self_check.py path/to/diagram.html
```

输出 `OK` 表示通过。加上 `--offline` 还会在仍存在 Google Fonts 链接时报错。更严格的几何检查：

```bash
python3 skills/diagram-design/scripts/verify_geometry.py path/to/diagram.html
```

如果你已经进入 `skills/diagram-design/` 目录，把命令前缀换成 `python3 scripts/...` 即可。

环境是否就绪（尤其是 PNG 导出所需的 Playwright），可以运行 `doctor`：

- Claude Code：`/diagram-design:doctor`
- Pi / Factory Droid：`/doctor`
- 任意宿主：直接说 “run diagram-design doctor”

`doctor` 是只读诊断，不会替你安装依赖。

---

## 5. 支持的 50 种图表

每种类型在仓库里都有一份 `type-*.md` 参考，以及浅色、深色、完整编辑风三套示例。名称后括号内是仓库中的英文类型名，方便你查参考文件或在提示词里点名。

### 5.1 软件架构与数据系统

| 中文名 | 类型 | 适合画什么 |
|---|---|---|
| 架构图 | `architecture` | 组件 + 连接的整体快照；可在同一张图里嵌套管理域与网络边界 |
| 架构变更图 | `architecture-delta` | 同步的“变更前 · 变更 · 变更后”拓扑，附新增/删除/修改/移动/重连清单 |
| IT 现状图 | `it-state` | 按阶段或部门展示遗留 IT 版图，强调“现在是什么样” |
| 部署图 | `deployment` | 区域、主机、制品、副本、端口：软件到底跑在哪里 |
| 依赖图 | `dependency` | 谁依赖谁，包含扇入与环；有弧线图（arc diagram）变体 |
| 高层数据栈 | `high-level` | 端到端数据栈落在一个容器集群上的全景 |
| 数据流 | `data-flow` | 按角色划分的数据管道步骤，谁在每一步做什么 |
| Medallion 分层 | `medallion` | 多层级数据存储（青铜/白银/黄金）与质量等级、访问策略 |
| 数据平台集成 | `dp-integration` | 数据平台集成拓扑：数据源 → 核心 → 消费方 |
| 数据平台安全矩阵 | `dp-security-matrix` | 按角色/组件展示访问权限矩阵 |
| 数据库模式图 | `db-schema` | 物理表：SQL 类型、约束、索引、列级外键 |
| ER / 数据模型 | `er` | 实体、字段与关系 |
| UML 类图 | `uml-class` | 类、操作、继承、组合等带类型的关系 |

### 5.2 流程、逻辑与状态

| 中文名 | 类型 | 适合画什么 |
|---|---|---|
| 流程图 | `flowchart` | 带分支的决策逻辑 |
| 时序图 | `sequence` | 参与者之间按时间排序的消息 |
| 状态机 | `state` | 状态、迁移与守卫条件 |
| 多角色流程 | `process` | 多个参与者、带数据交接的顺序流程 |
| 泳道图 | `swimlane` | 跨职能流程与交接 |
| 步骤图 | `steps` | 一段简短、有序的方法，带编号、图标与简要说明 |
| 事件建模 | `event-modeling` | 命令 → 事件 → 读模型，按有序阶段展开 |
| 需求追踪 | `requirement-traceability` | 需求 → 设计 → 验证，用稳定 ID 连接，缺口显式标出 |
| 故事地图 | `story-map` | 叙事主干切成发布切片，并画出切割线 |
| 甘特图 | `gantt` | 任务与阶段在时间轴上的排布 |

### 5.3 层级、关系与组织

| 中文名 | 类型 | 适合画什么 |
|---|---|---|
| 嵌套图 | `nested` | 用包含关系表达层级或作用域 |
| 树形图 | `tree` | 父 → 子关系 |
| 组织架构图 | `org-chart` | 人/团队/智能体的归属、汇报、路由与升级 |
| 分层堆叠 | `layers` | 层层叠加的抽象层级 |
| 韦恩图 | `venn` | 集合之间的重叠 |
| 金字塔 / 漏斗 | `pyramid` | 排名层级或转化流失 |
| 循环 / 飞轮 | `loop` | 最后一步回到第一步、中心累积状态的增强回路 |
| 单位网格 | `unit-grid` | 一个有限整体按 100 个等分单位计数 |

### 5.4 分析、策略与体验

| 中文名 | 类型 | 适合画什么 |
|---|---|---|
| 四象限 | `quadrant` | 双轴定位/优先级；有 BCG/麦肯锡风格的顾问变体 |
| 雷达图 | `radar` | 多个对象在 3–5 个量化维度上的对比 |
| Wardley 地图 | `wardley` | 价值链 × 演化：该自建、该购买、什么正在移动 |
| 鱼骨图 | `fishbone` | 一个观察到的结果，按类别归组的原因（根因分析） |
| 看板 | `kanban` | 按状态展示进行中的工作，含 WIP 上限与阻塞项 |
| 用户旅程 | `journey` | 用户在各阶段做什么，以及感受如何 |
| 服务蓝图 | `service-blueprint` | 客户可见的前台与服务隐藏的后台，共享阶段网格与可见性线 |
| 时间轴 | `timeline` | 事件在时间上的位置 |

### 5.5 数据可视化

| 中文名 | 类型 | 适合画什么 |
|---|---|---|
| 柱状图 | `bar` | 分类之间的量化对比；有哑铃图（dumbbell）变体 |
| 折线图 | `line` | 随时间变化的趋势；还有 slopegraph、ridgeline、streamgraph、bump 变体 |
| 散点图 | `scatter` | 两个变量的相关/分布；有气泡图、beeswarm、箱线图变体 |
| 矩形树图 | `treemap` | 以面积表达整体中的部分；有 marimekko 变体 |
| 热力图 | `heatmap` | 行 × 列交叉表的数值，用填充色编码 |
| 桑基图 | `sankey` | 数量在阶段之间分裂与合并，带宽 = 数量 |
| 极坐标图 | `polar` | 一个量化序列跨越周期性类别：角度 = 类别，半径 = 数值 |
| 瀑布图 | `waterfall` | 从起始总量经过带符号的贡献到达结束总量（预算桥、人数变化） |

### 5.6 空间与专业领域

| 中文名 | 类型 | 适合画什么 |
|---|---|---|
| 爆炸轴测图 | `exploded` | 一个物体沿单一轴拆开：拆解、开箱、装配顺序 |
| 轴测平面图 | `axonometric-plan` | 斜俯视看一层楼或一个场地：房间与家具，或按阶段标注的建筑群 |
| SBGN 生物过程图 | `sbgn` | 物种/过程二部图，含区室、消耗、生产、可逆与调控弧 |

> 统计口径说明：仓库当前有 50 份 `type-*.md` 参考，图库编号为 01–50；GitHub 仓库简介仍写着“45”，以技能内的实际类型为准。

---

## 6. 品牌定制：让图长得像你的网站

默认皮肤是“白烟纸 + 喷气黑 + 焦橙强调 + 蓝灰辅助”的编辑风，开箱即可截图。但真正的价值是让图匹配**你的**品牌。

### 6.1 默认设计 token

| 语义角色 | 用途 | 浅色默认值 | 深色默认值 |
|---|---|---|---|
| `paper` | 页面背景、默认节点填充 | `#f5f5f5` | `#2d3142` |
| `paper-2` | 图表容器背景、次级填充 | `#ececec` | `#393e53` |
| `ink` | 主文字、主描边 | `#2d3142` | `#f5f5f5` |
| `muted` | 次要文字、默认箭头 | `#4f5d75` | `#bfc0c0` |
| `soft` | 子标签、边界标签 | `#5a6580` | `#949eb2` |
| `rule` | 细线边框 | `rgba(45,49,66,0.12)` | `rgba(245,245,245,0.12)` |
| `accent` | 每张图 1–2 个焦点元素 | `#bf4520` | `#f08a59` |
| `accent-tint` | 强调描边盒的填充 | `rgba(191,69,32,0.08)` | `rgba(240,138,89,0.10)` |
| `link` | HTTP/API 调用、外部箭头 | `#2e5aa8` | `#739fdf` |

三套字体：

- **Instrument Serif**：页面标题、斜体旁注；
- **Geist（无衬线）**：节点名称、人类可读标签；
- **Geist Mono**：端口、协议、URL、字段类型等技术子标签，以及眉标和箭头标签。

### 6.2 从网站自动提取品牌

对助手说：

```text
Onboard diagram-design to https://yoursite.com
```

流程是：

1. 抓取你的首页（会先请求你的确认）；
2. 提取主色调与字体栈；
3. 映射到语义角色：`paper`、`ink`、`muted`、`accent`、`link`；
4. 展示一份 token diff，等你确认；
5. 写入 `references/style-guide.md`，或保存为具名 profile。

提取映射关系：

| 从网站检测到 | 变成 |
|---|---|
| `<body>` 背景色 | `paper` |
| 主要文字颜色 | `ink` |
| 次要/说明文字 | `muted` |
| 卡片或容器背景 | `paper-2` |
| 最常用的品牌色（CTA、链接、标题） | `accent` |
| `<h1>` 字体 | `title` 字体 |
| `<body>` 字体 | `node-name` 字体 |
| `<code>` / `<pre>` 字体 | `sublabel` 字体 |

写 token 之前会自动做 WCAG AA 对比度校验（正文 4.5:1，必要图形标记 3:1）。如果网站颜色在图表字号下不达标，会提出调整值并解释原因。品牌匹配还会给出一份“保真收据”：采样了哪些 URL、每个颜色角色、字体族与字重、字体来源、以及任何回退。

### 6.3 手动改

直接编辑 [`skills/diagram-design/references/style-guide.md`](https://github.com/cathrynlavery/diagram-design/blob/main/skills/diagram-design/references/style-guide.md) 里的表格。下游所有图表、注释原语和图库都只读语义角色（`accent`，而不是某个十六进制值），所以改这一处就能整体换肤。

硬性约束：

- 只保留**一个**强调色；两个强调色等于没有焦点。
- 文字 token 在 `paper` 上必须达到 4.5:1。
- 字体最多三个族：衬线 + 无衬线 + 等宽。
- 纸色是暖中性色，不要纯白。
- 阴影不要；边框用 1px 细线；圆角最大 10px；所有结构坐标、盒子尺寸与间距落在 4px 网格。

### 6.4 多客户：具名 profile

为不同客户保存不同皮肤，避免来回改安装目录里的 `style-guide.md`：

- Profile 库：`~/.diagram-design/profiles/`
- 单个 profile：`~/.diagram-design/profiles/<slug>.md`
- 项目标记：项目根目录的 `.diagram-design`

标记文件整个内容只有一行：

```text
profile: <slug>
```

`<slug>` 必须匹配 `[a-z0-9][a-z0-9-]{0,63}`（小写字母、数字、连字符，最长 64 字符）。带标记的项目会直接读取对应 profile，不同项目可以并行使用不同品牌。`default` 是内置保留名称，不能覆盖。

管理命令：

- Claude Code：`/diagram-design:profile`
- Factory Droid / Pi：`/profile`
- 其他宿主：用自然语言说“保存/切换/列出 profile”

常用动作：`list`、`save [name]`、`load [name]`、`show`、`update [name]`、`reset`、`delete [name]`。

### 6.5 离线与字体

默认字体来源是 `web`：加载 Google Fonts（唯一网络请求）。如果要求“离线/系统字体/不要 Google Fonts”，把风格指南里的 `Font source` 设为 `system`：

- 完全去掉字体 `<link>`；
- 每个 `font-family` 栈仍保留通用族，已安装 Geist 的人仍会用 Geist，其他人回退到系统衬线/无衬线/等宽；
- 导出时用 `--system-fonts`，自检时用 `self_check.py --offline` 确认没有残留 Google Fonts 链接。

想兼顾“没有网络”和“编辑字体”，可以在本机安装 Instrument Serif、Geist、Geist Mono（三者都是 SIL Open Font License），再把来源设为 `system`。

### 6.6 中文标签

- 繁体中文使用 `'Geist', 'Noto Sans TC', 'PingFang TC', 'Microsoft JhengHei', sans-serif`；标题用 `'Instrument Serif', 'Noto Serif TC', serif`。
- 简体中文同理，回退到 `'Noto Sans SC'`、`'PingFang SC'`、`'Microsoft YaHei'`。简体字体不在默认 Google Fonts 链接里，会走本机字体。
- 中文节点名称字号**不低于 12px**；放不下就删字，不要缩字号。
- 端口、URL、字段类型等技术子标签保持拉丁文与等宽字体。
- 箭头标签/眉标/图例这类 7–8px 等宽槽位，遇到中文要切换为 12px 无衬线、500 字重、不加字距、不做大写转换，并相应扩大遮罩矩形。

---

## 7. 导入已有图表

已有 draw.io、Mermaid、Excalidraw 或 PlantUML 图？把源文件指给助手，它会**重画**：保留内容（组件、关系、分组、方向），丢弃源坐标、源配色、源字体和渲染器布局。

### 7.1 支持的文件

| 来源 | 接受的文件 |
|---|---|
| draw.io | `.drawio`、`.drawio.xml`、`.xml`、内嵌图表的 `.drawio.png`、`.drawio.svg` |
| Mermaid | `.mmd`、`.mermaid`、Markdown 中的 `mermaid` 围栏代码块 |
| Excalidraw | `.excalidraw`、`.excalidraw.json` 场景文件（不接受导出的 PNG/SVG） |
| PlantUML | `.puml`、`.plantuml`、`.pu`、Markdown 中的 `plantuml` / `puml` 围栏代码块；仅支持时序图与类图 |

解析只读文本：不渲染、不执行 JavaScript、不联网、不运行 PlantUML 运行时，也不会跟随源文件里的点击链接。

### 7.2 命令示例

Claude Code：

```text
/diagram-design:import-drawio platform.drawio
/diagram-design:import-drawio platform.drawio --size=slide-16x9 --detail=simplified --audience=executive
/diagram-design:import-drawio platform.drawio --detail=faithful --format=png --page=all
/diagram-design:import-mermaid README.md --diagram=all
/diagram-design:import-mermaid architecture.mmd --size=slide-16x9 --detail=simplified
/diagram-design:import-excalidraw whiteboard.excalidraw --size=slide-16x9 --detail=simplified
/diagram-design:import-plantuml sequence.puml
/diagram-design:import-plantuml classes.puml --size=slide-16x9 --detail=simplified
```

也可以直接说：“把这张 draw.io 重画成演示文稿版本”“把这段 Mermaid 做成编辑风”“把白板草图整理得能见人”。

### 7.3 四个旋钮

导入前必须先定这四件事，因为它们会改变布局、字号、节点数和措辞：

| 旋钮 | 选项 | 作用 |
|---|---|---|
| **Format** 格式 | `html` · `svg` · `png` · `html+png` | 交付物。SVG 给 Figma，PNG 给幻灯片，HTML 给网页 |
| **Size** 尺寸 | `doc-inline` · `doc-wide` · `slide-16x9` · `slide-4x3` · `social-og` · `social-square` · `print-a4-landscape` · `print-a3-landscape` · `print-letter-landscape` · `fit` | 改变 `viewBox` **和字号梯度**：投影幻灯片用 16px 节点名，而不是 12px |
| **Detail** 细节 | `faithful`（≤24 节点，分区） · `balanced`（≤12） · `simplified`（≤7） | 源内容保留多少，按固定降级阶梯：装饰 → 重复项 → 叶子簇 → 基础设施 |
| **Audience** 受众 | `engineer` · `mixed` · `executive` | 改的是**措辞**，不是数量：`Auth Service / JWT · RS256 · :8443` → `Auth Service / token check` → `Sign-in` |

默认值：`html`、`doc-inline`、`balanced`、`mixed`、浅色变体。

### 7.4 保真度清单

每次导入结束，助手会报告一份 fidelity ledger，说明合并、折叠、丢弃了什么：

```text
Detail: balanced · 12 source nodes → 8 drawn
Collapsed: "Token valid?" decision → edge label on Gateway → Auth
Dropped:   1 sticky note ("legacy path, to be retired"), unconnected in source
Kept in full: the request path (Web/Mobile → Gateway → Orders → Postgres)
```

不会带过来：源坐标、源配色、源字体、draw.io 的斜线连接、Mermaid 的自动布局、Excalidraw 的手绘几何、PlantUML 的 skinparam。

一定会保留：组件、关系、分组、方向。导入是有边界的：不发明组件来填版面，也不悄悄丢掉组件。

### 7.5 尺寸预设

| 预设 | viewBox | 用途 |
|---|---|---|
| `doc-inline`（默认） | 960 × 600 | 正文宽度的文档/README 插图 |
| `doc-wide` | 1280 × 720 | 全宽文档、wiki 页面 |
| `slide-16x9` | 1280 × 720 | 16:9 演示文稿 |
| `slide-4x3` | 1024 × 768 | 旧版 4:3 模板 |
| `social-og` | 1200 × 632 | 链接预览卡片 |
| `social-square` | 1080 × 1080 | 信息流/轮播方图 |
| `print-a4-landscape` | 1120 × 792 | A4 横向打印 |
| `print-a3-landscape` | 1584 × 1120 | A3 横向打印 |
| `print-letter-landscape` | 1056 × 816 | US Letter 横向 |
| `fit` | 按内容推导 | 矢量交付，无固定画框 |

尺寸预设同时决定字号梯度（标准 / 演示 / 打印），不是只改画布大小。

---

## 8. 导出 SVG / PNG

图默认以自包含 HTML 交付；需要给 Figma、幻灯片或社交卡片时再导出。

Claude Code：

```text
/diagram-design:export-diagram path/to/diagram.html
/diagram-design:export-diagram path/to/diagram.html --svg-only
/diagram-design:export-diagram path/to/diagram.html --png-only --scale=3
/diagram-design:export-diagram path/to/diagram.html --registry
```

Pi：

```text
/export-diagram path/to/diagram.html
/export-diagram path/to/diagram.html --svg-only
/export-diagram path/to/diagram.html --png-only --scale=3
/export-diagram path/to/diagram.html --registry
```

也可以直接说：“把这张图导出成 SVG 和 PNG”“把 my-diagram.html 存成 PNG”。

要点：

- 不带格式参数时，**同时**生成 `.svg` 和 `.png`，放在源文件旁边。
- `--svg-only` / `--png-only` 只生成一种；两者互斥。
- `--scale=N` 控制 PNG 缩放，范围 1–4（含小数，如 1.25），默认 2。
- `--output=<path>` 指定输出基名，扩展名自动追加。
- `--registry` 生成 `<basename>.registry.json`，只对使用“可追踪块分解”语义模式的图有意义；单独使用 `--registry` 时只生成 JSON，不生成图片，也不需要 Playwright。与 `--svg-only` / `--png-only` 组合才同时出图。
- **PNG 依赖 Playwright**。一次性安装：

  ```bash
  python -m pip install playwright
  python -m playwright install chromium
  ```

  没安装时命令会给出安装提示并停止，不会自动安装。

- SVG 会提取 `<svg>` 节点并注入 Google Fonts，使其在浏览器、Figma、Illustrator 中独立可渲染；加 `--system-fonts` 可跳过字体导入，适合离线。
- 两种格式都**只包含图本身**：完整编辑风的标题、卡片等编辑性外框不会进入导出。要整页截图，用浏览器的打印为 PDF 或整页截图。
- 动画 HTML 导出时，导出明确的一帧：打开 `?motion=static`，等待 `document.fonts.ready`，确认运动根节点带有 `data-frame="static"` 再截图。

---

## 9. 命令、配置与目录速查

### 9.1 斜杠命令

| 命令（Claude Code） | Pi | 作用 |
|---|---|---|
| `/diagram-design:import-drawio` | 用自然语言（无内置 drawio 提示模板） | 重画 draw.io |
| `/diagram-design:import-mermaid` | `/import-mermaid` | 重画 Mermaid |
| `/diagram-design:import-excalidraw` | `/import-excalidraw` | 重画 Excalidraw |
| `/diagram-design:import-plantuml` | `/import-plantuml` | 重画 PlantUML（时序/类图） |
| `/diagram-design:export-diagram` | `/export-diagram` | 导出 SVG/PNG |
| `/diagram-design:profile` | `/profile` | 管理客户 profile |
| `/diagram-design:doctor` | `/doctor` | 环境诊断 |

Factory Droid 同样提供 `/profile`；其他命令面以宿主实际安装的插件为准。

通用安装（`npx skills add`）只装 Agent Skill，不包含这些原生命令；需要命令面时请用对应宿主的插件市场。

### 9.2 目录结构（简化）

```text
diagram-design/
├── skills/diagram-design/
│   ├── SKILL.md                  # 哲学、类型选择、检查清单
│   ├── references/
│   │   ├── style-guide.md        # 颜色/字体/token 的唯一事实来源
│   │   ├── semantic-patterns.md  # 行为模式（与布局无关）
│   │   ├── animation.md          # 可选动画与无障碍契约
│   │   ├── onboarding.md         # URL → token 的流程
│   │   ├── profiles.md           # 具名客户 profile 与项目标记
│   │   ├── import-*.md           # 四种来源的重画流程
│   │   ├── output-spec.md        # 格式 × 尺寸 × 细节 × 受众
│   │   ├── export.md             # SVG/PNG 导出
│   │   ├── primitives-*.md       # 注释、图标、手绘、终端等原语
│   │   └── type-*.md             # 50 种类型各自的布局语法
│   ├── scripts/                  # 提取器、导出、自检、几何校验
│   └── assets/                   # 图库、模板、示例
├── commands/                     # Claude 插件命令
├── prompts/                      # Pi 提示模板
├── docs/cookbook.md              # 操作配方：安装、首图、品牌、导入、导出、校验
├── docs/adr/                     # 已定设计决策记录
└── CHANGELOG.md                  # 版本历史
```

### 9.3 什么时候加载什么

| 你说的话 | 助手加载 |
|---|---|
| “画一张流程图” | `SKILL.md` + `type-flowchart.md` |
| “画架构图” | `SKILL.md` + `type-architecture.md` |
| “展示这次迁移新增/删除/修改了什么” | `SKILL.md` + `type-architecture-delta.md` |
| “把设备内部拆开展示” | `SKILL.md` + `type-exploded.md` |
| “画我们办公室的平面图” | `SKILL.md` + `type-axonometric-plan.md` |
| “两条策略请求为什么结果不同” | `SKILL.md` + `semantic-patterns.md` + `type-flowchart.md` |
| “给这张策略追踪图加动画” | 之前的选择 + `animation.md` |
| “把技能配置到我的网站” | `SKILL.md` + `onboarding.md` + `style-guide.md` |
| “用我保存的 Acme 客户 profile” | `SKILL.md` + `profiles.md` + 对应 profile |
| “重画这个 .drawio 给演示文稿用” | `SKILL.md` + `import-drawio.md` + `output-spec.md` + 目标类型参考 |

### 9.4 校验命令

在仓库根目录运行（如果已进入 `skills/diagram-design/`，把前缀换成 `scripts/`）：

```bash
python3 skills/diagram-design/scripts/self_check.py path/to/diagram.html
python3 skills/diagram-design/scripts/self_check.py --offline path/to/diagram.html
python3 skills/diagram-design/scripts/verify_geometry.py path/to/diagram.html
```

维护者的 CI 门禁（仓库根目录）：

```bash
python scripts/lint-skin.py path/to/diagram.html
python scripts/verify-geometry.py path/to/diagram.html
python scripts/verify-docs-sync.py
```

---

## 10. 设计规则：它为什么好看

这一节解释“编辑风”为什么稳定，也解释为什么助手会拒绝你的一些要求。

### 10.1 最高质量的编辑动作通常是删除

- 每个节点代表一个独立概念；总是一起出现的两个节点应合并。
- 每条连接都要携带信息；如果布局已经说明关系，就删掉线。
- 强调色是**编辑性**的，不是报警系统：每张图只给 1–2 个焦点。
- 目标密度是 **4/10**：技术上完整，但不至于需要导读。
- 超过 9 个节点，通常应该拆成两张图。

### 10.2 复杂度预算

| 限制 | 规则 |
|---|---|
| 最大节点数 | 9 |
| 最大箭头/迁移数 | 12 |
| 最大强调元素 | 2 |
| 最大旁注 | 2 |
| 动画（可选） | 最多 8 步、12 个标记项、2 个同时项 |

超了就拆成“总览 + 细节”。

### 10.3 连接线六条硬规则

1. **只能正交**：不同轴的节点之间用 `r=8` 的圆角折线（紧凑布局最小 `r=6`），只有两端共享 x 或 y 时才用直线；斜线直接失败。
2. **标签留白**：箭头标签最多 14 个字符、全大写、居中，放在不透明遮罩上，与线保持 6–10px 可见间隙，且在线段旁边而不是压线。
3. **不重叠**：平行路线至少偏移 12px；交叉处用桥/跨线。
4. **共享边扇出**：同一条边上的多个连接点各占一个位置，间距至少 12px（小盒子 8px）。
5. **不穿过非端点盒子**：必须穿过时用虚线 `4,3`，标签放在可见端，且不在中间盒子上加箭头。
6. **遮罩先于节点**：标签遮罩不能压住后画的节点。

### 10.4 字体与视觉

- 标题和斜体旁注用 Instrument Serif；节点名用 Geist 600；技术子标签用 Geist Mono。
- 等宽字体只用于技术内容（端口、命令、URL），不是“开发者氛围”的万能字体；**永远不要 JetBrains Mono**。
- 1px 细线；无阴影；圆角最大 10px；纸色为暖中性。
- 结构几何落在 4px 网格；字号按角色梯度，不跟网格。

---

## 11. 常见问题与限制

**Q：输出质量稳定吗？**

不稳定。质量取决于你使用的模型；同一个提示两次布局可能不同。它不是确定性的排版引擎。

**Q：能像 draw.io 那样继续拖拽编辑吗？**

不能。输出是 HTML + 内联 SVG，没有可拖拽的画布，也没有可在 PR 里 diff 的源文本。SVG 可以在 Figma/Illustrator 里继续矢量编辑，但布局不是“源格式”。

**Q：那什么时候该用 Mermaid？**

图要放在 Git 仓库里、每个 sprint 都变、需要 diff 和 GitHub 原生渲染时，Mermaid 更合适。Diagram Design 面向“要正式发布的那一张图”。

**Q：为什么只让我放 9 个节点？**

这是复杂度预算。超过预算时更好的做法是拆成总览 + 细节，而不是把字缩小。

**Q：需要联网吗？**

默认只请求 Google Fonts。设置为系统字体后完全离线。品牌 onboarding 需要抓取你提供的网站首页。

**Q：中文标签会出问题吗？**

技能支持中文，但要注意：简体中文字体不在默认 Google Fonts 链接里，会回退到本机字体；中文节点名不低于 12px；技术子标签保持拉丁文；箭头标签/眉标/图例遇到中文要切换字号与字距规则。如果要求所有查看者机器上渲染一致，需要确保查看环境安装了对应的 Noto 字体，或自行把 Noto Sans SC 加入字体链接。

**Q：PNG 导出报错？**

先安装 Playwright 与 Chromium：

```bash
python -m pip install playwright
python -m playwright install chromium
```

**Q：托管安装会覆盖我改过的风格吗？**

可能会。插件更新可能替换 `references/style-guide.md`。想长期保留品牌，用 `~/.diagram-design/profiles/` 里的 profile + 项目 `.diagram-design` 标记；想直接改工作副本，就用可编辑安装（克隆仓库 + 软链）。

**Q：怎么更新？**

- `npx skills` 安装：`npx skills update diagram-design`
- Claude Code：在市场里开自动更新，或更新插件
- Codex：`codex plugin marketplace upgrade diagram-design`
- Copilot：`copilot plugin marketplace update diagram-design` + `copilot plugin update diagram-design@diagram-design`
- Droid：`droid plugin marketplace update diagram-design` + `droid plugin update diagram-design@diagram-design --scope user`
- Pi：`pi update --extensions`
- Kiro / OpenCode：重新导入或替换目录

**Q：这是官方版本吗？**

官方构建只来自 [cathrynlavery/diagram-design](https://github.com/cathrynlavery/diagram-design)。LittleMight（作者公司）发布插件目录列表；其他名称下的列表是非官方副本。网络请求说明见仓库的 `PRIVACY.md`。

**Q：许可证？**

MIT。作者 Cathryn Lavery。

---

## 12. 参考链接

- 仓库：[https://github.com/cathrynlavery/diagram-design](https://github.com/cathrynlavery/diagram-design)
- 官网：[https://diagramdesign.dev](https://diagramdesign.dev)
- 在线图库：[https://cathrynlavery.github.io/diagram-design/](https://cathrynlavery.github.io/diagram-design/)
- 更新日志：[CHANGELOG.md](https://github.com/cathrynlavery/diagram-design/blob/main/CHANGELOG.md)
- 操作配方：[docs/cookbook.md](https://github.com/cathrynlavery/diagram-design/blob/main/docs/cookbook.md)
- 设计系统：[skills/diagram-design/SKILL.md](https://github.com/cathrynlavery/diagram-design/blob/main/skills/diagram-design/SKILL.md)
- 风格指南：[skills/diagram-design/references/style-guide.md](https://github.com/cathrynlavery/diagram-design/blob/main/skills/diagram-design/references/style-guide.md)
- 输出规格：[skills/diagram-design/references/output-spec.md](https://github.com/cathrynlavery/diagram-design/blob/main/skills/diagram-design/references/output-spec.md)
- 贡献指南：[CONTRIBUTING.md](https://github.com/cathrynlavery/diagram-design/blob/main/CONTRIBUTING.md)
- 隐私说明：[PRIVACY.md](https://github.com/cathrynlavery/diagram-design/blob/main/PRIVACY.md)
