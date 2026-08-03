---
slug: box-content-polish
status: plan-written
intent: clear
review_required: false
pending-action: none (plan written to .omo/plans/box-content-polish.md; execution awaits user /start-work)
approach: Option A 就地美化 — 定义无框无底色 shellinbox 代码样式消除框内双框撞色；3 处 infobox 表格用 arraystretch/arrayrulecolor/padding 撑满留白；跨约 10 处嵌套站点应用；xelatex 双次编译验证无回归。
---

# Draft: box-content-polish

## Components (topology ledger)
<!-- Lock the SHAPE before depth. One row per top-level component that can succeed or fail independently. -->
<!-- id | outcome (one line) | status: active|deferred | evidence path -->

| id | outcome | status | evidence |
|----|---------|--------|----------|
| C1 shellinbox 样式 | 在导言区定义 `\lstdefinestyle{shellinbox}`（继承 shell 但去 frame/bg/margins），作为框内代码的统一去框开关 | active | Git_VSCode_Tutorial.tex:74-84（shell 样式定义处，在其后追加） |
| C2 infobox 内代码（6 处） | 6 处 infobox+lstlisting 改用 `[style=shellinbox]`，消除蓝框套灰框 | active | Git_VSCode_Tutorial.tex:630-640, 1411-1422, 2610-2627, 2629-2642, 2644-2656, 2658-2676 |
| C3 stepbox 内代码（1 处 4 块） | 1016 stepbox enumerate 内 4 个 lstlisting 改用 shellinbox，统一无框观感 | active | Git_VSCode_Tutorial.tex:1016-1057 |
| C4 infobox 内表格（3 处） | 3 处 infobox+tabular 加 `\arraystretch{1.3}`+`\arrayrulecolor{black}`+局部 `\tabcolsep`；infobox 调 boxsep/left/right 留白 | active | Git_VSCode_Tutorial.tex:420-432, 448-461, 669-683 |
| C5 几何/留白微调 | 给含表格的 infobox 加 `boxsep=2mm, left=6mm, right=6mm`（或逐处权衡），让表格不窘迫 | active | tcolorbox 几何键 p.46-50 |
| C6 编译验证 | xelatex 连续两次编译，确认无致命错误、无新增 Overfull 回归 | active | AGENTS.md 构建命令节 |

## Open assumptions (announced defaults)
<!-- Intent is CLEAR: record any default you adopt instead of asking, so the user can veto it at the gate. -->
<!-- assumption | adopted default | rationale | reversible? -->

| assumption | adopted default | rationale | reversible? |
|------------|-----------------|-----------|--------------|
| 表格用 plain tabular 还是 tabularx 键 | **plain tabular + 手动 \arraystretch/\arrayrulecolor**，不引入 tabularx 键 | TeX.SE 380361 证实 tabularx 键内部自动注入 \arrayrulecolor{tcbcol@frame}+\def\arraystretch{1.1}，会劫持 booktabs 规则色与行距；plain+手动控制更稳、风险更低 | 是 |
| 1016 stepbox 代码是否一并处理 | **一并套用 shellinbox** | stepbox 虽无外框，但灰框灰底代码夹在粗体「操作步骤」标题与编号步骤间仍显沉重；统一去框与 infobox 一致 | 是 |
| 是否动代码字体/字号 | **不动 basicstyle，仅去 frame/backgroundcolor/margins** | 改字体会波及全篇代码观感，超出"框内美化"范围；保留 shell 的 basicstyle 维持一致 | 是 |
| 是否加载 tcolorbox listings 库 | **不加载**（A 约束） | AGENTS.md「不得新增环境类型」；tcblisting 是日后放宽约束的升级项，已记录备查 | 是 |
| 表格行距/列距具体值 | arraystretch=1.3，tabcolsep=6pt（booktabs 惯例） | TeX.SE 与 tcolorbox 文档常见值；可在编译后微调 | 是 |

## Findings (cited - path:lines)

- `Git_VSCode_Tutorial.tex:30-39` — 颜色定义（codebg 248,248,248；codeframe 200,200,200；infoblue 21,101,192；warnred 198,40,40）
- `Git_VSCode_Tutorial.tex:52-72` — `\lstset` 全局 frame=single + backgroundcolor=\color{codebg} + rulecolor=\color{codeframe}（双框根因）
- `Git_VSCode_Tutorial.tex:74-84` — `\lstdefinestyle{shell}`（与 lstset 一致，shellinbox 追加其后）
- `Git_VSCode_Tutorial.tex:86-129` — 5 个 box 定义（tipbox/keybox/stepbox=\newenvironment 无框；warnbox/infobox=\newtcolorbox 白底彩框）
- `Git_VSCode_Tutorial.tex:420-432, 448-461, 669-683` — infobox+tabular 三处
- `Git_VSCode_Tutorial.tex:630-640, 1411-1422, 2610-2627, 2629-2642, 2644-2656, 2658-2676` — infobox+lstlisting 六处
- `Git_VSCode_Tutorial.tex:1016-1057` — stepbox+lstlisting（enumerate 内 4 块）
- tcolorbox 6.10.0 手册：§17.1.1 p.335（listings 库加载）、§17.3 p.339（newtcblisting）、§17.5 p.345（listing only）、几何 p.46-50、before/after skip p.93
- `tcblistings.code.tex:35-40` — tcblatex 样式无 frame/backgroundcolor/xleftmargin（证明 tcblisting 默认不画内框；升级路径备查）
- TeX.SE 742472（`backgroundcolor={}` 空值删除已定义背景，让框 colback 透出）
- TeX.SE 380361（tabularx 键劫持 \arrayrulecolor/\arraystretch；修复用 tabularx* 变体或改 plain tabular）
- TeX.SE 394401（yshifttext=0pt 修标题间距）

## Decisions (with rationale)

1. **采用 Option A 就地美化**（用户 m0009 选定）——最小改动、合规于 AGENTS.md「不得新增环境类型」、保留 callout 与代码的语义耦合。
2. **定义 `\lstdefinestyle{shellinbox}`（样式，非新环境）**——继承 shell，覆盖 `frame=none, backgroundcolor={}, xleftmargin=0pt, xrightmargin=0pt, aboveskip=0pt, belowskip=0pt`。TeX.SE 742472 证实 `backgroundcolor={}` 删除已定义背景让框 colback 透出，比"匹配同色"更稳（改 colback 自动同步）。
3. **infobox 表格用 plain tabular + 手动控制**，不引入 tabularx 键——规避 TeX.SE 380361 的 booktabs 劫持陷阱；局部 `\renewcommand{\arraystretch}{1.3}` + `\arrayrulecolor{black}` + `\setlength{\tabcolsep}{6pt}`。
4. **stepbox 代码一并套用 shellinbox**——统一去框观感，与 infobox 一致。
5. **不加载 listings 库 / 不用 tcblisting**——严守 A 约束；tcblisting 升级路径已记录于 librarian 简报，日后放宽约束时可迁移。

## Scope IN

- 导言区（`\lstdefinestyle{shell}` 之后，约行 84）追加 `\lstdefinestyle{shellinbox}` 定义
- 10 处嵌套 lstlisting（6 infobox + 4 stepbox）改 `[style=shell]`→`[style=shellinbox]`
- 3 处 infobox 内 tabular 加局部 `\arraystretch`/`\arrayrulecolor`/`\tabcolsep`
- 3 处含表格的 infobox 调几何键 `boxsep`/`left`/`right`（可选延展至 6 处含代码 infobox）
- xelatex 连续两次编译，确认无致命错误、对比 Overfull/Underfull 警告无显著回归
- 同步更新 AGENTS.md「文档撰写规范」节，记录 shellinbox 样式与表格进框约定

## Scope OUT (Must NOT have)

- 不新增 `\newtcolorbox`/`\newenvironment`/`\newtcblisting` 环境类型（AGENTS.md 禁止）
- 不加载 tcolorbox listings 库（超出 A 范围）
- 不改 box 颜色、图标、标签文本（AGENTS.md 禁止）
- 不把代码/表格移出框外（Option B 已否决）
- 不新增章节、不改章节结构
- 不修改 `sc-*.md` 参考文档（只读）
- 不改代码 basicstyle/字体/字号（仅去 frame/bg/margins）
- 不自动 git 提交

## Open questions

无——策略分叉已于 m0009 由用户选定 Option A。

## Approval gate
status: approved-plan-written
approach: Option A 就地美化——定义 shellinbox 无框代码样式（去 frame/bg/margins，backgroundcolor={} 让框色透出）消除框内双框撞色；3 处 infobox 表格用 arraystretch/arrayrulecolor/tabcolset + box 几何键撑满留白（plain tabular 规避 tabularx 键的 booktabs 劫持）；跨约 10 处嵌套站点应用；xelatex 双次编译验证无回归；同步更新 AGENTS.md。
next workflow action: 已完成——计划已写入 .omo/plans/box-content-polish.md（9 个 todo + 4 项最终验证，4 波次依赖矩阵）。执行由用户独立启动（如 /start-work），本 planner 不执行。
