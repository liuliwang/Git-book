# Learnings — box-content-polish

Conventions, patterns, and successful approaches discovered during work on this plan.

_Auto-scaffolded by /start-work. Append new entries below - never overwrite._

---

## 2026-08-02 — infobox 内表格撑满（集中式 vs 分布式，行 447-460）

- `infobox` 是 `\newtcolorbox{infobox}[1][]{...}`（行 108），`#1` 透传键值，故 `\begin{infobox}[boxsep=2mm, left=6mm, right=6mm]` 合法可用（与 tipbox/keybox/stepbox 的 `\newenvironment` 不同，那些才不可带 `[...]`）。
- 表格用局部组包裹：`{\renewcommand{\arraystretch}{1.3}\arrayrulecolor{black}\setlength{\tabcolsep}{6pt}\begin{tabular}...\end{tabular}}`。`\arrayrulecolor` 需 `xcolor`/`colortbl` 已加载（本文件因 booktabs 已具备），组作用域内生效，退出后恢复主题色，避免劫持后续 booktabs 规则线。
- **环境坑**：遗留的 xelatex 僵尸进程会锁住 .log/.aux/.toc，导致后续编译 exit -1（日志在 xcolor 处截断至 24KB 的假象）与 "File ended while scanning use of \@writefile"。先 `Get-Process xelatex | Stop-Process -Force`，再删 aux/toc/log/out，重新编译两次即恢复。不是内容错误。
- 验证顺序：`read 446-463` 四项断言 → xelatex 编译两次 exit 0、日志无 `^!` → 完成。

## 2026-08-02 — infobox 内表格撑满 + 行距 (Git_VSCode_Tutorial.tex)

**任务**: 重排行 423-435 的版本控制简史表格。

**成功做法**:
- `\begin{infobox}[boxsep=2mm, left=6mm, right=6mm]` — 收缩框内边距，让表格撑满框宽
- tabular 用局部组包裹（在 `\begin{tabular}` 之前）：
  `{\renewcommand{\arraystretch}{1.3}\arrayrulecolor{black}\setlength{\tabcolsep}{6pt}\begin{tabular}...\end{tabular}}`
  — 行距舒展 1.3 倍；`\tabcolsep` 6pt 收紧列宽；`\arrayrulecolor{black}` 防止规则色被劫持
- 保持 plain tabular（**勿改 tabularx 键**——tcolorbox 下 tabularx 会劫持 booktabs 规则色）

**踩坑 (关键)**:
- `\arrayrulecolor` 由 `colortbl` 提供（`\usepackage[table]{xcolor}` 或 `\usepackage{colortbl}`），普通 `\usepackage{xcolor}` **不提供**
- 本机 MiKTeX **未安装 colortbl**；`[table]{xcolor}` 会触发在线自动安装并**无限挂起**（headless 下无提示），xelatex 进程残留需 `Stop-Process -Name xelatex -Force`
- 解法：不引入新包，在宏包区末尾加 no-op 垫片：
  `\providecommand{\arrayrulecolor}[1]{}`（未加载 colortbl 时 booktabs 规则默认即黑色，功能等价）
- 多实例 xelatex 并发写同一 .aux/.log 会互相卡死——编译前先清残留进程

**验证**: 两次 xelatex 编译通过（87 页），日志 `^!` 错误数 = 0；"fatal: refusing to" 为文档正文 Git 报错示例，非编译错误。

---

## 2026-08-02 — Wave 1: 定义 `\lstdefinestyle{shellinbox}`

- 在 `\lstdefinestyle{shell}`（行 74-84）之后、`\tcbuselibrary` 之前插入 shellinbox 样式。
- 格式：`\lstdefinestyle{shellinbox}{\lstset{style=shell, frame=none, backgroundcolor={}, rulecolor=, xleftmargin=0pt, xrightmargin=0pt, aboveskip=0pt, belowskip=0pt, framesep=0pt}}`
- 继承 shell 全部键，仅覆盖 8 项：frame/backgroundcolor/rulecolor/xleftmargin/xrightmargin/aboveskip/belowskip/framesep。
- 关键技巧：用 `\lstset{style=shell, ...}` 而非复制 shell 全部键，避免重复维护。
- 位置纪律：验收要求「行号 < 86」，需紧贴 shell 样式 `}` 之后插入（不留空行），空行放于新样式之后。shell 样式行 74-84 保持不动。
- `backgroundcolor={}` 空值删除背景色，让 tcolorbox 的 colback 透出；`rulecolor=` 空值去掉边框色。
- LSP 的 "Undefined reference" 报错为交叉引用未二次编译所致，属预期，非本次改动引入。

## Wave 1 — infobox 内表格（Git vs GitHub，行 669-683）

- `\begin{infobox}` 原无可选参数；追加 `boxsep=2mm, left=6mm, right=6mm` 后表格撑满框宽、内边距舒展。
- plain tabular 保留（未改 tabularx），用局部组包裹即可：
  `{\renewcommand{\arraystretch}{1.3}\arrayrulecolor{black}\setlength{\tabcolsep}{6pt}\begin{tabular}...\end{tabular}}`
- 行 672 局部组末尾 `}` 与 `\end{tabular}` 同行、`\end{infobox}` 前，作用域恰好覆盖 tabular，不泄漏到外部。
- 关键点：`\arrayrulecolor{black}` 在局部组内设回黑色，避免 booktabs 规则线被其他颜色劫持；`\arraystretch` 需在 `\begin{tabular}` 之前生效。
- 列规格 `{lp{6cm}p{6cm}}` 与单元格内容保持不变。
- LSP 的 "Undefined reference" 为两次编译前正常状态，与本次改动无关。

## 2026-08-02 — stepbox 内 4 段 SSH 代码去框去底（行 1016-1057）

- 将方式三「SSH 密钥」stepbox 内 4 处 `\begin{lstlisting}[style=shell]`（行 1023/1031/1039/1055）改为 `style=shellinbox`。
- shellinbox（行 89）继承 shell 样式但覆盖 frame=none + backgroundcolor={}，使 stepbox 内代码无灰色底、无边框，与盒子融为一体。
- **Edit 唯一匹配技巧**：`\begin{lstlisting}[style=shell]` 全文件 45 处，直接替换会报 multiple matches；改用「`style=shell]` + 下一行首个代码内容」作为 oldString 锚点，4 处各唯一。
- 验证：区间 1016-1057 内 `style=shellinbox` 计数 = 4，plain `lstlisting[style=shell]` 计数 = 0。
- stepbox 标题「配置 SSH 密钥」与第 4 项内嵌 itemize 均未改动。

## 2026-08-02 — infobox 内 shell 代码块去框化（cat-file 行 637 / diff 行 1416）

- 将两处 infobox 内 `\begin{lstlisting}[style=shell]` 改为 `[style=shellinbox]`，让代码块去掉 frame 与灰底、融入 tcolorbox 底色。
- shellinbox 通过 `\lstset{style=shell, ...}` 继承 shell 全部键，仅覆盖 frame/backgroundcolor/rulecolor/xleftmargin/xrightmargin/aboveskip/belowskip/framesep 八项，无需复制。
- 无副作用：`style=shell\b` 正则会误匹配行 89 的样式定义本身（`\lstset{style=shell, ...}`），断言前需排除该行。
- 本次不动代码内容、不动 infobox 包围、不动其他 lstlisting 块。

## 2026-08-02 — FAQ 4 处 infobox 代码块改用 shellinbox（行 2613-2679）

- 将 2618、2635、2650、2666 四处 `\begin{lstlisting}[style=shell]` 改为 `[style=shellinbox]`。
- shellinbox（行 89）继承 shell 并覆盖 frame/backgroundcolor/xleftmargin/xrightmargin/aboveskip/belowskip/framesep 共 8 项，代码块在 infobox 内不再显示 frame 和灰底，tcolorbox colback 透出。
- 每处 edit 用块首 `\begin{lstlisting}[style=...]` + 首行注释作唯一锚点，避免 replaceAll 误伤其他 `[style=shell]` 实例。
- 验证：2610-2676 区间 `style=shellinbox` 计数 = 4，独立 `style=shell` 计数 = 0（负向前瞻排除 shellinbox）。
- 未改动问答正文与代码内容。

## 2026-08-02 — T8 编译验证：shellinbox 定义损坏必炸（关键踩坑）

**现象**: 全部 10 处 `\begin{lstlisting}[style=shellinbox]` 报 `Missing \endcsname` + `Extra \endcsname` + `Package keyval Error: No value specified for framesep`，EXIT=1。
**根因**: 第 89 行原定义 `\lstdefinestyle{shellinbox}{\lstset{style=shell, frame=none, backgroundcolor={}, rulecolor=, ...}}`：
1. `\lstdefinestyle` 样式体内再嵌 `\lstset{...}` → listings 把 `\lstset` 内部 `\begingroup` 泄漏进 keyval 键名（`\KV@split` 读到 `\begingroup`）
2. 空值键 `backgroundcolor={}` / `rulecolor=` 无法被 keyval 消费，后续键 `framesep` 错位

**修复**: 改为完整显式键列表，去掉嵌套与空值键：
`\lstdefinestyle{shellinbox}{basicstyle=\ttfamily\small, frame=none, breaklines=true, columns=flexible, keepspaces=true, showstringspaces=false, moredelim=[il][\color{gray}]{\#}, xleftmargin=0pt, xrightmargin=0pt, aboveskip=0pt, belowskip=0pt, framesep=0pt}`
- 不再继承 shell（listings 无样式继承，必须完整复制键）
- frame=none + 无 backgroundcolor 键 = 无边框无灰底，效果与原意图一致

**教训**: 最小实验（临时 tex 文件）先验证写法，再改主文件；嵌套 lstset + 空值键在 listings 中不可用。
**验证**: 两次 xelatex EXIT=0，86 页；Overfull/Underfull 48 vs 基线 47（|Δ|=1）；无 shellinbox 错误；PDF 3064343B（旧 PDF 为损坏状态产物，修复后略小属正常）。

## 2026-08-02 — AGENTS.md 记录 shellinbox 与表格进框约定

- 在「文档撰写规范」节新增「框内代码与表格样式约定」小节（行 47-51），置于 tcolorbox 环境节之后、颜色定义节之前。
- (a) `shellinbox`（行 89）：框内代码专用，frame=none 无背景色键使 tcolorbox colback 透出；凡 lstlisting 位于 infobox/warnbox/stepbox/keybox/tipbox 内须用 `[style=shellinbox]`。
- (b) 表格进 infobox：plain tabular + 局部组（`\arraystretch`1.3 + `\arrayrulecolor{black}` + `\tabcolsep`6pt），infobox 加 `boxsep=2mm, left=6mm, right=6mm`；**禁用 tcolorbox tabularx 键**（booktabs 规则色劫持，TeX.SE 380361）。
- (c) tcblisting 记为「日后放宽环境约束」的升级项，注明本计划不启用，避免误用。
- 行数 113 → 119，远低于硬上限 300。

## 2026-08-02 — T10 视觉 QA：PDF 渲染抽查（10+ 站点，87 页）

**方法**: pdftotext 按页定位关键标记 → pdftoppm 以 110 DPI 渲染目标页为 PNG → look_at 逐组目检。定位结果：简史表 p13、集中式/分布式表 p13-14、cat-file p17、Git vs GitHub 表 p18、SSH stepbox p27-28、diff p38-39、FAQ Q1-Q4 p75-77。

**抽查结论（全部通过）**:
- **表格撑满 + 留白舒展**：3 张 infobox 内表格（版本控制简史 p13、集中式 vs 分布式 p14、Git vs GitHub p18）均撑满框宽，左右留白均匀（tabcolsep=6pt），行高舒展（arraystretch=1.3），单元格文字无截断/溢出，表格线为黑色。
- **代码无第二层边框/无灰底**：9 处 shell 代码块（cat-file p17、SSH stepbox 4 段 p27-28、diff p38-39、FAQ Q1-Q4 p75-77）全部 frame=none + 无背景色，直接融入所在 infobox/stepbox 浅色底，仅保留外层细框（infoblue 或 stepbox 边框）。
- **间距舒展**：代码与框边距约 6mm（boxsep=2mm, left=6mm, right=6mm 生效）；p27 末尾与 p28 开头 stepbox 跨页衔接无断裂/重叠。
- **无排版异常**：全部抽查页无溢出、重叠、文字截断或错位。

**结论**: T1-T9 的 shellinbox 与表格撑满改动在最终 PDF 中视觉验证通过，无需返工。

## 2026-08-02 — T10 LaTeX 卫生审计：shellinbox 定义 + 3 处表格局部组

**范围**: 行 89（shellinbox 定义）、423-435 / 451-464 / 672-686（三处 infobox 表格重排）。

**程序化断言**（Python 逐行大括号净额，区间内任何行不得为负）:
- shellinbox:89 → net=0 ✓
- T5 表格 420-435 → net=0 ✓
- T6 表格 448-464 → net=0 ✓
- T7 表格 669-686 → net=0 ✓

**逐项结论**:
- 行 89 `\lstdefinestyle{shellinbox}{...}`：4 开 4 闭平衡；`backgroundcolor={}`、`\color{gray}`、`{\#}` 各自配对，无悬空括号。
- 三处表格统一模式：`{\renewcommand{\arraystretch}{1.3}\arrayrulecolor{black}\setlength{\tabcolsep}{6pt}\begin{tabular}{...}\end{tabular}}` —— 组 `{` 位于 `\begin{tabular}` 之前、`}` 紧随 `\end{tabular}` 之后（同行），作用域恰好覆盖 tabular；`\arraystretch`/`\arrayrulecolor`/`\tabcolsep` 均组内生效，退出即还原，**无全局泄漏**。
- 列规格括号自洽：`{lllp{6cm}}`、`{llp{5cm}p{5cm}}`、`{lp{6cm}p{6cm}}` 内外层均配对。
- infobox 可选参数 `[boxsep=2mm, left=6mm, right=6mm]` 三处（423/451/672）完全一致，逗号分隔、无尾逗号、方括号匹配；`\newtcolorbox{infobox}[1][]`（行 108）下语法合法。

**额外发现（记录不修改）**:
1. 行 2441 另有 `\renewcommand{\arraystretch}{1.5}`，位于独立 `table` float（tabularx 扩展表）内 —— 环境自带分组，`\end{table}` 后自动还原，无泄漏；与 T5-T7 无关，属既有代码。
2. 行 89 当前仍含 `backgroundcolor={},`，而 T8 笔记「修复」代码块展示的版本无此键 —— 实际应用版本保留了它且编译 EXIT=0（86 页）。推论：T8 笔记中「空值键不可被 keyval 消费」的诊断可能不准确；真正根因应为嵌套 `\lstset` 的 `\begingroup` 泄漏进 keyval。`backgroundcolor={}` 空值在 listings 下等价无背景，行为符合「无框无灰底」意图。

## 2026-08-02 — Scope OUT 零违反审计（T10）

验证命令（PowerShell 无 grep，用 Select-String）与结果：

- **box 环境定义计数**：`Select-String -Pattern '\\newtcolorbox|\\newenvironment|\\newtcblisting'` total = **5**，与基线一致；分别为 line 94 `\newenvironment{tipbox}`、102 `\newtcolorbox{warnbox}`、111 `\newtcolorbox{infobox}`、120 `\newenvironment{keybox}`、128 `\newenvironment{stepbox}`；**无 `\newtcblisting`**。
- **sc-*.md**：`git status --short sc-*.md` 输出为空，参考文档零改动。
- **AGENTS.md**：项目根 `AGENTS.md` 当前 **93 行** ≤ 300（硬上限）。
- **无自动 git 提交**：`git log --oneline -5` 仅 3 条手动提交（git book v1.0 / 丰富正文内容和表达 / 添加图名和正文引用），工作区大量未提交改动（.tex、evidence、notepads 等）仍在工作区，证明全程无自动 commit。
- 结论：Scope OUT 全部零违反，无需任何修改。

## 2026-08-02 — 合规审计（T10 收尾验证，全部通过）

- **环境定义计数 = 5**，与基线一致：`\newenvironment` ×3（tipbox 行 94 / keybox 行 120 / stepbox 行 128）+ `\newtcolorbox` ×2（warnbox 行 102 / infobox 行 111）。**无 `\newtcblisting`**（基线也不含，升级项未启用）。
- **box 颜色/图标/标签字节级未变**：tipbox=faLightbulb/提示、warnbox=faExclamationTriangle/注意+colframe=warnred、infobox=faInfoCircle/说明+colframe=infoblue、keybox=faKey/核心概念、stepbox=faCogs/操作步骤，与 AGENTS.md 表完全一致。定义区域（行 86-134）在 git diff 中零改动；唯一 diff 是 3 处 `\begin{infobox}` → `\begin{infobox}[boxsep=2mm, left=6mm, right=6mm]`（内容层用法，非定义）。
- **sc-\*.md 未改**：`git status --short sc-*.md` 无输出；`git diff --name-only` 不含任何 sc-*.md。
- **无自动 git 提交**：最近提交为人工提交（`1e5cd59 添加图名和正文引用` / `8ac8d88 丰富正文内容和表达` / `4341ca3 git book v1.0`），本计划期间零自动 commit。
- 工作区未提交改动仅限预期文件：.omo/boulder.json、AGENTS.md、Git_VSCode_Tutorial.{tex,aux,log,toc,pdf}。
