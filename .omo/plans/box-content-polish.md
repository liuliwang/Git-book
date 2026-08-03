# box-content-polish - Work Plan

## TL;DR (For humans)

**What you'll get:** 教程里那些蓝框套灰框、显得乱的代码块，以及挤在框里留白窘迫的表格，会变得干净统一——代码不再有自己的额外边框和灰底（融入所在提示框的底色），表格在框里撑满、行距舒展。

**Why this approach:** 用一个"框内专用"的代码样式去掉代码自带的边框和灰底（让框的底色透出来），表格则调行距/列距并避开一个已知的 booktabs 冲突陷阱。最小改动、不引入新框型、不动现有 5 个提示框的定义。

**What it will NOT do:** 不新增任何提示框类型；不改框的颜色/图标/标签文字；不把代码或表格挪到框外面；不动代码字体字号；不修改 sc-*.md 参考文档；不自动提交 git。

**Effort:** Short
**Risk:** Low - 局部样式与逐处替换，回退只需还原 style 名
**Decisions to sanity-check:** 表格用 plain tabular + 手动控制（而非 tcolorbox 的 tabularx 键，规避 booktabs 劫持陷阱）；1016 stepbox 的代码一并套用 shellinbox（虽无外框，但灰框仍显沉重）。

Your next move: 由独立 worker 执行（如 `/start-work`）；或先跑高精度评审复核计划本身。完整执行细节见下。

---

> TL;DR (machine): Short effort, Low risk — define shellinbox no-frame code style + restyle 3 infobox tables (arraystretch/arrayrulecolor/padding, plain tabular to avoid booktabs hijack), apply across ~10 nested sites, 2x xelatex verify, update AGENTS.md.

## Scope
### Must have
- 导言区定义 `\lstdefinestyle{shellinbox}`（继承 shell，去 frame/bg/margins；`backgroundcolor={}` 让框 colback 透出）
- 10 处嵌套 lstlisting（6 infobox + 4 stepbox）改用 `[style=shellinbox]`
- 3 处 infobox 内 tabular 加局部 `\arraystretch{1.3}` + `\arrayrulecolor{black}` + `\tabcolsep=6pt`；infobox 调 `boxsep/left/right` 留白
- xelatex 连续两次编译通过、无新增致命错误
- AGENTS.md 记录 shellinbox 样式与表格进框约定（≤300 行）

### Must NOT have (guardrails, anti-slop, scope boundaries)
- 不新增 `\newtcolorbox`/`\newenvironment`/`\newtcblisting` 环境类型
- 不加载 tcolorbox listings 库（超出 A 范围）
- 不改 box 颜色、图标、标签文本
- 不把代码/表格移出框外（Option B 已否决）
- 不改代码 basicstyle/字体/字号
- 不修改 `sc-*.md` 参考文档
- 不自动 git 提交

## Verification strategy
> Zero human intervention - all verification is agent-executed.
- Test decision: tests-after（LaTeX 无单测；以 grep 断言 + xelatex 编译 + PDF 视觉验证为三层证据）
- Evidence: `.omo/evidence/task-<N>-box-content-polish.<ext>`（attemptDir = `.omo/evidence/`，非 ulw-loop 场景）

## Execution strategy
### Parallel execution waves
- **Wave 1（4 并行）**：T1（定义 shellinbox）+ T5/T6/T7（3 处表格重排，与 T1 互不依赖）
- **Wave 2（3 并行，阻塞于 T1）**：T2（630+1411）、T3（FAQ 4 块）、T4（stepbox 4 块）套用 shellinbox
- **Wave 3（1，阻塞于 T1-T7）**：T8 两次 xelatex 编译 + 警告对比（集成闸门）
- **Wave 4（1，阻塞于 T8）**：T9 更新 AGENTS.md

### Dependency matrix
| Todo | Depends on | Blocks | Can parallelize with |
| --- | --- | --- | --- |
| T1 | — | T2, T3, T4, T8 | T5, T6, T7 |
| T2 | T1 | T8 | T3, T4 |
| T3 | T1 | T8 | T2, T4 |
| T4 | T1 | T8 | T2, T3 |
| T5 | — | T8 | T1, T6, T7 |
| T6 | — | T8 | T1, T5, T7 |
| T7 | — | T8 | T1, T5, T6 |
| T8 | T1-T7 | T9 | — |
| T9 | T8 | — | — |

## Todos
> Implementation + Test = ONE todo. Never separate.
<!-- APPEND TASK BATCHES BELOW THIS LINE WITH edit/apply_patch - never rewrite the headers above. -->
- [x] 1. 定义 `\lstdefinestyle{shellinbox}` 样式（导言区）
  What to do / Must NOT do: 在 `\lstdefinestyle{shell}` 块之后（Git_VSCode_Tutorial.tex:84 之后、box 定义 86 之前）追加 `\lstdefinestyle{shellinbox}[]`，继承 shell 全部排版键（basicstyle/keywordstyle/commentstyle/stringstyle/showstringspaces/breaklines/columns/moredelim 与 shell 完全一致），仅覆盖：`frame=none`、`backgroundcolor={}`（空值删除全局 codebg 让框 colback 透出，TeX.SE 742472）、`rulecolor=`、`xleftmargin=0pt`、`xrightmargin=0pt`、`aboveskip=0pt`、`belowskip=0pt`、`framesep=0pt`。Must NOT：改 basicstyle/字体、新增环境、加载新包、修改既有 `shell` 样式（74-84 字节级不变）。
  Parallelization: Wave 1 | Blocked by: — | Blocks: T2, T3, T4, T8
  References (executor has NO interview context - be exhaustive): Git_VSCode_Tutorial.tex:52-72（\lstset 全局 frame=single+backgroundcolor，双框根因）、74-84（\lstdefinestyle{shell}，在其后插入）、30-39（颜色 codebg/codeframe/infoblue）；TeX.SE 742472（backgroundcolor={} 删除已定义背景的规范技巧）；tcolorbox tcblistings.code.tex:35-40 佐证"框内代码不应自带 frame/bg"
  Acceptance criteria (agent-executable): `grep -n "lstdefinestyle{shellinbox}" Git_VSCode_Tutorial.tex` 恰好 1 处匹配且行号 < 86；该样式定义内含 `frame=none` 与 `backgroundcolor={}`；`diff` 对 74-84 行（shell 样式）与基线一致。
  QA scenarios (name the exact tool + invocation): happy = 上述 grep 三项断言全过；failure = 单次 `xelatex Git_VSCode_Tutorial.tex`（语法校验，单遍足矣）退出码 0 且 log 中无 "Undefined control sequence"/"Missing }" 涉及 shellinbox。Evidence `.omo/evidence/task-1-box-content-polish.txt`（grep 输出 + log 末 50 行）
  Commit: Y | style(preamble): add shellinbox code style for box-embedded listings

- [x] 2. 套用 shellinbox 到 infobox 内代码 630 与 1411
  What to do / Must NOT do: 在 Git_VSCode_Tutorial.tex:630-640（infobox 包裹 cat-file 对象查看演示）与 1411-1422（infobox 包裹 diff 格式解读）两处，将内层 `\begin{lstlisting}[style=shell]` 改为 `\begin{lstlisting}[style=shellinbox]`。Must NOT：移动代码到 infobox 外、编辑代码内容、触碰周围 `\begin{infobox}...\end{infobox}` 与正文文字、改其他 lstlisting 块。
  Parallelization: Wave 2 | Blocked by: T1 | Blocks: T8 | Can parallelize with: T3, T4
  References: Git_VSCode_Tutorial.tex:630-640, 1411-1422（执行前先读 625-645 与 1405-1425 确认 infobox begin/end 完整包裹该 lstlisting）
  Acceptance criteria: `grep -n "style=shellinbox" Git_VSCode_Tutorial.tex` 在 630-640 与 1411-1422 两区间各有 ≥1 匹配；`grep -c "style=shell\b"`（非 inbox，\b 词界）在这两区间合计 = 0。
  QA scenarios: happy = grep 双断言；failure = read 625-645 + 1405-1425 确认 infobox begin/end 未破损。Evidence `.omo/evidence/task-2-box-content-polish.txt`
  Commit: Y | style(content): neutralize code frame inside infobox at §3 cat-file and §5 diff

- [x] 3. 套用 shellinbox 到 FAQ infobox 代码 2610-2676（4 块 Q1-Q4）
  What to do / Must NOT do: 在 Git_VSCode_Tutorial.tex:2610-2627、2629-2642、2644-2656、2658-2676 四处 Q&A infobox，各将内层 `\begin{lstlisting}[style=shell]` 改为 `[style=shellinbox]`。共 4 处独立编辑，模式同 T2。Must NOT：改动问答正文、移动代码出框、触碰其他 FAQ 条目。
  Parallelization: Wave 2 | Blocked by: T1 | Blocks: T8 | Can parallelize with: T2, T4
  References: Git_VSCode_Tutorial.tex:2610-2627, 2629-2642, 2644-2656, 2658-2676（执行前读 2605-2680 确认 4 个 infobox begin/end 配对）
  Acceptance criteria: `grep -c "style=shellinbox" Git_VSCode_Tutorial.tex` 在 2610-2676 行区间 = 4；`grep -c "style=shell\b"` 在该区间 = 0。
  QA scenarios: happy = grep 计数双断言；failure = read 2605-2680 确认 4 个 infobox 完整。Evidence `.omo/evidence/task-3-box-content-polish.txt`
  Commit: Y | style(faq): neutralize code frame inside Q1-Q4 infoboxes

- [x] 4. 套用 shellinbox 到 stepbox 代码 1016-1057（enumerate 内 4 块）
  What to do / Must NOT do: 在 Git_VSCode_Tutorial.tex:1016-1057（stepbox 包裹 enumerate，每个 \item 后跟一个 lstlisting），将 4 个 `\begin{lstlisting}[style=shell]` 改为 `[style=shellinbox]`。Must NOT：重构 enumerate、编辑代码内容、触碰 stepbox 标题与「操作步骤」标签。
  Parallelization: Wave 2 | Blocked by: T1 | Blocks: T8 | Can parallelize with: T2, T3
  References: Git_VSCode_Tutorial.tex:1016-1057（4 个 lstlisting 块位于 \begin{stepbox}...\end{stepbox} 内的 \begin{enumerate} 中；执行前读 1010-1060 确认结构）
  Acceptance criteria: `grep -c "style=shellinbox" Git_VSCode_Tutorial.tex` 在 1016-1057 = 4；`grep -c "style=shell\b"` 在该区间 = 0。
  QA scenarios: happy = grep 计数双断言；failure = read 1010-1060 确认 stepbox+enumerate begin/end 完整。Evidence `.omo/evidence/task-4-box-content-polish.txt`
  Commit: Y | style(ssh): neutralize code frame inside SSH-key stepbox steps

- [x] 5. 重排 infobox 内表格 420（版本控制简史）
  What to do / Must NOT do: 在 Git_VSCode_Tutorial.tex:420-432（infobox 包裹版本控制简史 tabular），将 `\begin{tabular}...\end{tabular}` 用局部组包裹：`{\renewcommand{\arraystretch}{1.3}\arrayrulecolor{black}\setlength{\tabcolsep}{6pt}\begin{tabular}...\end{tabular}}`；并给外层 `\begin{infobox}[...]` 增补几何键 `boxsep=2mm, left=6mm, right=6mm`（若已有可选参数则合并，否则新增）。**保留 plain tabular，不得改为 tabularx 键**（TeX.SE 380361：tabularx 键劫持 \arrayrulecolor/\arraystretch 使 booktabs 规则变框线色）。Must NOT：转 tabularx 键、改表格内容、改 infobox 标签/图标。
  Parallelization: Wave 1 | Blocked by: — | Blocks: T8 | Can parallelize with: T1, T6, T7
  References: Git_VSCode_Tutorial.tex:420-432（执行前读 418-434 看清当前 tabular 列规格与 infobox 可选参数现状）；tcolorbox 几何键手册 p.46-50（boxsep 加在 left/right/top/bottom 之上；left/right 默认 4mm，top/bottom 默认 2mm）；TeX.SE 380361（tabularx 键劫持 booktabs 的根因）
  Acceptance criteria: read 418-434 确认 (a) infobox 调用含 `boxsep=2mm`；(b) `\arraystretch{1.3}` 与 `\arrayrulecolor{black}` 出现在 `\begin{tabular}` 之前；(c) tabular 列规格与单元格内容不变；(d) 仍为 plain tabular（无 `tabularx` 键）。
  QA scenarios: happy = read 四项断言；failure = 单次 `xelatex` 退出 0 且 log 在 420 行附近无 "Missing }"/"Extra }"/表错误。Evidence `.omo/evidence/task-5-box-content-polish.txt`
  Commit: Y | style(ch1): improve version-history table padding inside infobox

- [x] 6. 重排 infobox 内表格 448（集中式 vs 分布式）
  What to do / Must NOT do: 同 T5 模式，作用于 Git_VSCode_Tutorial.tex:448-461。局部组 + 几何键。保留 plain tabular。Must NOT 同 T5。
  Parallelization: Wave 1 | Blocked by: — | Blocks: T8 | Can parallelize with: T1, T5, T7
  References: Git_VSCode_Tutorial.tex:448-461（执行前读 446-463）；几何键 p.46-50；TeX.SE 380361
  Acceptance criteria: read 446-463 确认四项同 T5(a-d)。
  QA scenarios: happy = read 断言；failure = 单次 xelatex 退出 0、448 附近无表错误。Evidence `.omo/evidence/task-6-box-content-polish.txt`
  Commit: Y | style(ch1): improve centralized-vs-distributed table padding inside infobox

- [x] 7. 重排 infobox 内表格 669（Git vs GitHub）
  What to do / Must NOT do: 同 T5 模式，作用于 Git_VSCode_Tutorial.tex:669-683。保留 plain tabular。Must NOT 同 T5。
  Parallelization: Wave 1 | Blocked by: — | Blocks: T8 | Can parallelize with: T1, T5, T6
  References: Git_VSCode_Tutorial.tex:669-683（执行前读 667-685）；几何键 p.46-50；TeX.SE 380361
  Acceptance criteria: read 667-685 确认四项同 T5(a-d)。
  QA scenarios: happy = read 断言；failure = 单次 xelatex 退出 0、669 附近无表错误。Evidence `.omo/evidence/task-7-box-content-polish.txt`
  Commit: Y | style(ch2): improve Git-vs-GitHub table padding inside infobox

- [x] 8. xelatex 连续两次编译 + 警告对比（集成闸门）
  What to do / Must NOT do: 先跑一次基线编译（若 Git_VSCode_Tutorial.log 已陈旧）并 `grep -c "Overfull\|Underfull" Git_VSCode_Tutorial.log` 记录基线计数。然后连续执行两次 `xelatex Git_VSCode_Tutorial.tex`（AGENTS.md：首遍建 .aux/.toc，次遍解析 \ref/\label）。每次记录退出码。二次后再 `grep -c "Overfull\|Underfull"`。判定：两遍均退出 0、无 "fatal"、无 "Undefined control sequence" 涉及 shellinbox、Overfull/Underfull 计数差 |Δ| ≤ 2（cosmetic，AGENTS.md 允许）。Must NOT：单遍即宣告通过、忽略致命错误、自动 git 提交。
  Parallelization: Wave 3 | Blocked by: T1-T7 | Blocks: T9 | Can parallelize with: —
  References: AGENTS.md 构建命令节（MiKTeX 路径 `C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe`，须两遍）；AGENTS.md 备注「fatal: refusing to」是正文示例文本非编译错误，勿误判
  Acceptance criteria: 两遍 xelatex 退出码均 = 0；`grep -i "fatal\|Undefined control sequence" Git_VSCode_Tutorial.log | grep -i "shellinbox"` 输出为空；`Git_VSCode_Tutorial.pdf` mtime 更新且 size > 基线 size − 1KB。
  QA scenarios (name the exact tool + invocation): happy = 退出 0 + PDF 刷新 + 无 shellinbox 错误 + 警告 Δ ≤ 2；failure = 任一致命错误 → 抓取 log 中错误行前后各 100 行上下文，标记 FAIL，**不进入 T9**。Evidence `.omo/evidence/task-8-box-content-polish.log`（完整 log）+ `.omo/evidence/task-8-box-content-polish.txt`（摘要 + 基线/终态计数 diff）
  Commit: N | （仅验证，不提交）

- [x] 9. 更新 AGENTS.md 记录 shellinbox 与表格进框约定
  What to do / Must NOT do: 在项目根 AGENTS.md「文档撰写规范」节增补小节：(a) 记录 `shellinbox` 样式存在及其用途（框内代码专用，去 frame/bg 让框 colback 透出）；凡 lstlisting 位于 infobox/warnbox/stepbox/keybox/tipbox 内须用 `[style=shellinbox]`。(b) 表格进 infobox 须用 plain tabular + 局部 `\arraystretch{1.3}` + `\arrayrulecolor{black}` + `\tabcolsep=6pt`，infobox 设 `boxsep=2mm, left=6mm, right=6mm`；**禁用 tcolorbox 的 tabularx 键**（booktabs 劫持，TeX.SE 380361）。(c) tcblisting 为日后放宽「不得新增环境类型」约束的升级项，本计划不启用。保持 AGENTS.md ≤ 300 行（项目硬上限）。Must NOT：超 300 行、改 sc-*.md、改 box 颜色/图标/标签规则、把 tcblisting 写成当前可用。
  Parallelization: Wave 4 | Blocked by: T8 | Blocks: — | Can parallelize with: —
  References: AGENTS.md（项目根，「文档撰写规范」节）；Git_VSCode_Tutorial.tex:74-84（T1 后的 shellinbox 定义）；TeX.SE 380361
  Acceptance criteria: read AGENTS.md 确认新增小节存在且含 (a)(b) 两点；`wc -l AGENTS.md` ≤ 300。
  QA scenarios: happy = read 断言 + 行数 ≤ 300；failure = 若 > 300，在既有章节精简冗余（不得删新约定），重测。Evidence `.omo/evidence/task-9-box-content-polish.txt`
  Commit: Y | docs(agents): record shellinbox style + table-in-box convention

## Final verification wave
> Runs in parallel after ALL todos. ALL must APPROVE. Surface results and wait for the user's explicit okay before declaring complete.
- [x] F1. Plan compliance audit：复审 .omo/plans/box-content-polish.md vs AGENTS.md 约束——无新增环境类型（grep `\newtcolorbox\|\newenvironment\|\newtcblisting` 计数与基线一致）、box 颜色/图标/标签字节级不变、sc-*.md 未改（`git status sc-*.md` clean）、无自动 git 提交（`git log` 除非用户操作否则不变）。全过则 APPROVE。
- [x] F2. Code quality review：检查 shellinbox 定义（T1）与 3 处表格重排（T5-T7）的 LaTeX 卫生——大括号平衡、局部组闭合（`{...}` 配对）、无 \arraystretch 全局泄漏（局部组正确）、几何键语法合法。
- [x] F3. Real manual QA：渲染后的 `Git_VSCode_Tutorial.pdf`（T8 产物）抽查 10 处站点所在页（420/448/669/630/1411/1016/2610-2676）——用 `look_at` 读对应 PDF 页或 pdf 技能截页，视觉确认：框内代码无第二层边框/灰底（融入框底色）、表格撑满框宽且留白舒展、无溢出/截断。
- [x] F4. Scope fidelity：确认无新环境（grep 计数同基线）、sc-*.md 未动、AGENTS.md ≤ 300 行、未自动 git 提交；计划 Scope OUT 各项零违反。

## Commit strategy
- 每个编辑性 todo（T1-T7、T9）单独提交，conventional commit（`style(scope): ...` / `docs(agents): ...`）。T8 仅验证不提交。
- 或经用户同意后 squash 为单条 `style(tutorial): polish code/table appearance inside tcolorbox boxes`。
- **不自动 push**；用户先审 PDF 再决定是否推送。

## Success criteria
- 10 处嵌套站点在 PDF 中渲染无双框撞色 / 无表格窘迫。
- 两次 xelatex 编译退出 0、无新增致命错误、警告 Δ ≤ 2。
- 无新增环境类型；box 颜色/图标/标签字节级同基线。
- sc-*.md 未修改；AGENTS.md 已更新且 ≤ 300 行。
- 无自动 git 提交执行。
