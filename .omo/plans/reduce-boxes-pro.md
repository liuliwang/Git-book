# reduce-boxes-pro - Work Plan

## TL;DR (For humans)
<!-- Fill this LAST, after the detailed plan below is written, so it summarizes the REAL plan. -->
<!-- Plain English for a non-engineer: NO file paths, NO todo numbers, NO wave/agent/tool names. -->

**What you'll get:** 一份纯散文版的 Git/VS Code 教程，仅保留 8 个真正的不可逆操作警告框（warnbox），其余 89 个彩色文本框全部转为流畅的散文段落、编号列表、粗体标题和独立表格——格式干净，视觉专业，阅读无干扰。

**Why this approach:** 行业共识（O'Reilly、Google、Manning、Diátaxis）是文本框应该是稀缺资源，而非默认容器。Git 官方教程（gitcore-tutorial, Pro Git）本身就是纯散文驱动。保留 warnbox 是唯一被所有风格指南一致建议保留的框类型——因为它是安全护栏。

**What it will NOT do:** 不会修改任何章节结构、不会新增或删除内容、不会改动图片和 TikZ 图、不会引入新的 LaTeX 环境或样式定义、不会"顺便"改写 prose 语句、不会修改 warnbox 的样式或内容。

**Effort:** Large（89 个框的逐一手工转换 + 跨 10 章的编译验证）
**Risk:** Medium — 编译破坏风险通过增量检查点（每类型转换后编译两次）控制；交叉引用断裂风险通过 .aux 标签前后对比控制。
**Decisions to sanity-check:** 激进策略（仅保留 warnbox）已由你确认；表格提取为独立 table 已确认；学习目标转为粗体段落已确认。

Your next move: review the plan, then run `$start-work reduce-boxes-pro` to execute. Full execution detail follows below.

---

> TL;DR (machine): Large effort, Medium risk, converts 89 tcolorbox instances to prose across 10 chapters while preserving 8 warnbox warnings and all cross-references.

## Scope
### Must have
- 移除全部 `tipbox`、`keybox`、`stepbox`、`infobox` 环境（共 ~89 个实例）
- `stepbox` → 普通 `enumerate` 编号列表（保留 `\item` 和内部结构）
- `keybox` → 粗体小标题（`\textbf{...}`）+ 正文段落
- `tipbox` → 过渡引导句 + 自然融入正文的散文
- `infobox` → 散文段落 / 独立 `table` 环境（带 `\caption`+`\label`）/ 章节目标粗体段落
- 保留全部 8 个 `warnbox` 实例（内容、样式、颜色逐字节不变）
- 所有转换前后 `\label{...}` 键不丢失、交叉引用不出现 `??`
- 每次类型转换后 xelatex 编译两次验证（增量检查点）
- 表格编号和图编号保持与原文档完全一致

### Must NOT have (guardrails, anti-slop, scope boundaries)
- **不得**修改 `sc-*.md` 参考文档
- **不得**新增章节或修改章节结构
- **不得**新增 tcolorbox 环境或定义新的视觉样式
- **不得**新增 LaTeX 宏包依赖
- **不得**修改图片、TikZ 示意图、或用 `images/` 目录中的文件
- **不得**引入替代视觉分隔符（`\begin{quote}`、`\rule`、`\hrulefill`、缩进块等）
- **不得**"顺便"改写代码块内容或 prose 语句（仅移除 box begin/end 行 + 必要过渡词）
- **不得**清理"未使用"的颜色/样式定义（warnbox 仍需要它们）
- **不得**修改 warnbox 的任何内容和样式
- **不得**修改文档编译命令（仍用 xelatex 两次编译）

## Verification strategy
> Zero human intervention - all verification is agent-executed.
- Test decision: tests-after — 编译即测试，配以正则扫描 + .aux 标签对比
- Evidence: `.omo/evidence/<plan-slug>/` (每任务输出编译日志、diff、标签对比文件)

### 核心验证管线
1. **编译完整性**：每次修改后 xelatex ×2，退出码 0；`.log` 中无 "Emergency stop" 或 "Fatal error"
2. **Box 移除计数**：`Select-String -Pattern '\\begin\{(tipbox|keybox|stepbox|infobox)\}'` 各返回 0；`warnbox` 返回 8
3. **Warnbox 完整性**：`git diff` 中无 warnbox 相关行；warnbox 内容 SHA-256 前后一致
4. **交叉引用**：第二次编译后 `.log` 无 `LaTeX Warning: Reference.*undefined`；新增未定义引用数 ≤ 0
5. **标签稳定性**：`.aux` 中 `\newlabel{tab:...}` 和 `\newlabel{fig:...}` 的计数器和页码前后一致
6. **无新环境**：`git diff` 的 `+` 行中无未经批准的 `\begin{...}` 或 `\newcommand` 出现
7. **Overfull 预算**：与重构前基线相比，新增 overfull/underfull 警告 ≤ 5 条

## Execution strategy
### 转换顺序（从简单到复杂）
按类型分批：**stepbox → keybox → tipbox → infobox**。每批次完成后编译两次验证。infobox 最后处理（含表格、代码块、章节目标，是最复杂转换）。

### Parallel execution waves
> 逐类型串行（每类型内部可并行按章节分组）；编译检查点为类型级串行栅栏。

### Dependency matrix
| Todo | Depends on | Blocks | Can parallelize with |
| --- | --- | --- | --- |
| 1. 备份与证据目录 | — | 2-6 | — |
| 2. stepbox 转换 | 1 | 4 | 3（与 keybox 并行准备） |
| 3. keybox 转换 | 1 | 4 | 2 |
| 4. tipbox 转换 | 2, 3 | 5 | — |
| 5. infobox 转换 | 4 | 6 | — |
| 6. 最终验证 | 5 | — | F1-F4（并行运行） |

## Todos
> Implementation + Test = ONE todo. Never separate.
> 转换规则：每个框仅移除 `\begin{<type>}[...options...]` 和 `\end{<type>}` 行。内部内容保持原样。
<!-- APPEND TASK BATCHES BELOW THIS LINE WITH edit/apply_patch - never rewrite the headers above. -->

- [x] 1. 建立备份与验证基线
  What to do: 将 `Git_VSCode_Tutorial.tex` 复制到 `.omo/evidence/reduce-boxes-pro/original.tex`；运行两次 xelatex 编译，保存 `.log`、`.aux`、`.toc` 到 `.omo/evidence/reduce-boxes-pro/baseline/`；提取所有 `\label{...}` 键列表和 warnbox 内容 SHA-256；统计 Overfull/Underfull 警告数量为基线。
  Must NOT do: 修改任何源文件。
  Parallelization: Wave 1 | Blocked by: — | Blocks: 2-6
  References: `.omo/evidence/reduce-boxes-pro/` 目录；`Git_VSCode_Tutorial.tex` 全文。
  Acceptance criteria: `original.tex` 存在且 SHA-256 已知；基线 `.log` 中有编译成功标记；Overfull 警告基线已记录。
  QA scenarios:
  - Happy: 备份文件与源文件 SHA-256 一致；两次编译均以退出码 0 完成。
  - Failure: 编译失败 → 检查 xelatex 路径、残留进程锁（`taskkill /f /im xelatex.exe`）后重试。
  Evidence: `.omo/evidence/reduce-boxes-pro/baseline/`
  Commit: N（仅备份，不计入 git）

- [x] 2. 转换全部 stepbox 为普通 enumerate 列表
  What to do: 扫描全文 22 个 `stepbox` 实例。对每个实例：移除 `\begin{stepbox}`（及其可能的 `[options]`）和 `\end{stepbox}` 行；保留内部 `\begin{enumerate}...\end{enumerate}` 结构完全不变；在转换后的 enumerate 前后添加 `\medskip` 以保持视觉间距。完成后编译两次验证。
  Must NOT do: 不得修改 enumerate 内的 `\item` 内容；不得移除 `\begin{enumerate}` 或 `\end{enumerate}`；不得新增颜色或框。
  Parallelization: Wave 2 | Blocked by: 1 | Blocks: 4
  References: `Git_VSCode_Tutorial.tex` 中全部 `\begin{stepbox}` 行（见草稿行号列表）；`\medskip` 使用参考 `\newenvironment{stepbox}` 定义（行 128-134）
  Acceptance criteria: `Select-String -Pattern '\\begin\{stepbox\}' Git_VSCode_Tutorial.tex` 返回 0；xelatex ×2 退出码 0。编译日志无新增 fatal 错误。
  QA scenarios:
  - Happy: 所有 22 个 stepbox 的 enumerate 内容完整保留，编号连续无断号，无 `Lonely \item` 错误。
  - Failure: 编译失败 → `git diff` 定位转换引入的错误行，回滚该实例后重试。
  Evidence: `.omo/evidence/reduce-boxes-pro/task-2-stepbox/`（编译日志 + diff）
  Commit: Y | refactor(tex): convert 22 stepbox instances to plain enumerate lists

- [x] 3. 转换全部 keybox 为粗体散文段落
  What to do: 扫描全文 11 个 `keybox` 实例。对每个实例：移除 `\begin{keybox}` 和 `\end{keybox}` 行；将 `\textbf{<标题>}` 保留为段落首行（粗体）；其后的列表或正文保持原样。在转换后的段落前后添加 `\medskip`。
  Must NOT do: 不得修改 `\textbf` 内的文本；不得移除内部的 `\begin{enumerate}` 或 `\begin{itemize}` 结构。
  Parallelization: Wave 2 | Blocked by: 1 | Blocks: 4 | 可与 2 并行
  References: `Git_VSCode_Tutorial.tex` 中全部 `\begin{keybox}` 行；`\newenvironment{keybox}` 定义（行 120-126）
  Acceptance criteria: `Select-String -Pattern '\\begin\{keybox\}' Git_VSCode_Tutorial.tex` 返回 0；xelatex ×2 退出码 0。
  QA scenarios:
  - Happy: 11 个 keybox 的内容以粗体标题引导的段落形式呈现，列表编号完整。
  - Failure: 嵌套列表导致 `Lonely \item` → 检查 keybox 内部的 enumerate/itemize 是否完整。
  Evidence: `.omo/evidence/reduce-boxes-pro/task-3-keybox/`
  Commit: Y | refactor(tex): convert 11 keybox instances to bold-header prose paragraphs

- [x] 4. 转换全部 tipbox 为内联散文（第 0-4 章）
  What to do: 扫描第 0-4 章（行 1-1600）的 ~12 个 `tipbox` 实例。对每个实例：移除 `\begin{tipbox}` 和 `\end{tipbox}` 行；将内部内容保留为正文段落。对于引导性文字（如"提示"、"本章小结"、"成功标志"），转为 `\medskip\noindent\textbf{<引导词>：}\ ` 形式。对于纯提示性文字，直接融入周围段落或作为独立段落自然呈现。
  Must NOT do: 不得修改代码块；不得增删实质性内容；不得引入新的框环境。
  Parallelization: Wave 3 | Blocked by: 2, 3 | Blocks: 5
  References: `Git_VSCode_Tutorial.tex` 行 1-1600 中所有 `\begin{tipbox}` 行；`\newenvironment{tipbox}` 定义（行 94-100）
  Acceptance criteria: 第 0-4 章内 `\begin{tipbox}` 为 0；xelatex ×2 退出码 0；转换后的引导句读起来自然、无突兀断裂。
  QA scenarios:
  - Happy: 所有 tipbox 内容转化为自然散文，编译通过，无新增 overfull 警告。
  - Failure: 引导句与上下文不流畅 → 标记为 `[REVIEW]` 注释，不修改内容语义。
  Evidence: `.omo/evidence/reduce-boxes-pro/task-4-tipbox-ch0-4/`
  Commit: Y | refactor(tex): convert tipbox instances in chapters 0-4 to inline prose

- [x] 5. 转换全部 tipbox 为内联散文（第 5-8 章 + 附录）
  What to do: 同任务 4，处理第 5-8 章（行 1601-2795）和附录（行 ~3019+）的剩余 ~13 个 `tipbox` 实例。
  Must NOT do: 同任务 4。
  Parallelization: Wave 3 | Blocked by: 4 | Blocks: 6
  References: `Git_VSCode_Tutorial.tex` 行 1601+ 中所有 `\begin{tipbox}` 行
  Acceptance criteria: 全文 `\begin{tipbox}` 为 0；xelatex ×2 退出码 0。
  QA scenarios:
  - Happy: 同任务 4，全文档覆盖。
  - Failure: 同任务 4。
  Evidence: `.omo/evidence/reduce-boxes-pro/task-5-tipbox-ch5+/`
  Commit: Y | refactor(tex): convert remaining tipbox instances to inline prose

- [x] 6. 提取 infobox 中的表格为独立 table 环境
  What to do: 识别 3 个含 `\captionof{table}{...}` 的 `infobox[boxsep=2mm, left=6mm, right=6mm]` 实例（行 426、行 454、行 675）。对每个：移除 `\begin{infobox}[...]` 和 `\end{infobox}`；将内部的 `tabularx{\linewidth}` 改为 `tabularx{\textwidth}`（因为移除 infobox 后 linewidth 变大）；将 `\captionof{table}{...}\label{tab:xxx}` 提取为独立的 `\begin{table}[H]\centering\caption{...}\label{tab:xxx}` 开头和 `\end{table}` 结尾。如果表格周围有说明性 prose，保留为表格前后的正文段落。
  Must NOT do: 不得修改 `tabularx` 列规格（`l l l >{\raggedright\arraybackslash}X` 等）；不得修改 `\arraystretch` 或 `\tabcolsep` 设置；不得修改表格数据行。
  Parallelization: Wave 4 | Blocked by: 5 | Blocks: 7
  References: 行 426、454、675 的 infobox 实例；AGENTS.md 表格约定段落
  Acceptance criteria: 3 个表格均以独立 `\begin{table}...\end{table}` 存在，`\caption` 和 `\label` 位置正确；xelatex ×2 退出码 0；.aux 中 `\newlabel{tab:vc-history}`、`\newlabel{tab:centralized-vs-distributed}`、`\newlabel{tab:git-vs-github}` 与基线一致。
  QA scenarios:
  - Happy: 表格独立渲染，表号与重构前一致，`\ref{tab:...}` 正确解析。
  - Failure: 表号漂移 → 检查 table 计数器是否在转换中重复递增；使用基线 .aux 对比定位。
  Evidence: `.omo/evidence/reduce-boxes-pro/task-6-infobox-tables/`
  Commit: Y | refactor(tex): extract infobox tables to standalone table environments

- [x] 7. 转换 infobox 章节学习目标为粗体段落
  What to do: 扫描所有章节开头的 `\begin{infobox}` 内含 `\textbf{本章学习目标}` 的实例（约 8 个，分属第 1-7 章）。对每个：移除 `\begin{infobox}` 和 `\end{infobox}`；将 `\textbf{本章学习目标}` 改为 `\medskip\noindent\textbf{本章学习目标：}` 后紧跟 `\begin{enumerate}[leftmargin=1.5em]` 列表；列表后 `\medskip` 收尾。
  Must NOT do: 不得修改 enumerate 内的 `\item` 内容；不得将学习目标列表与后续 prose 合并。
  Parallelization: Wave 4 | Blocked by: 6 | Blocks: 8
  References: 章节开头含"本章学习目标"的 infobox（行 372、797、1105、1637、2025、2499 等）
  Acceptance criteria: 所有章节学习目标以 `\textbf{本章学习目标：}` + enumerate 形式存在；xelatex ×2 退出码 0。
  QA scenarios:
  - Happy: 每章学习目标以粗体标题引导的列表形式呈现，编译无错误。
  - Failure: enumerate 嵌套在已废弃的 infobox 残余中 → 逐章检查 `\begin{infobox}` 和 `\end{infobox}` 是否成对移除。
  Evidence: `.omo/evidence/reduce-boxes-pro/task-7-infobox-objectives/`
  Commit: Y | refactor(tex): convert chapter objective infoboxes to bold-header lists

- [x] 8. 转换 infobox 含代码块的实例为独立代码块
  What to do: 扫描全文 infobox 内包含 `\begin{lstlisting}[style=shellinbox]` 的实例（约 3-5 个，如行 571、636、648）。对每个：移除 `\begin{infobox}` 和 `\end{infobox}`；将 `[style=shellinbox]` 改为 `[style=shell]`（因为父 tcolorbox 已不存在，不再需要透传样式）；代码块前后说明性 prose 转为普通正文段落（保留 `\textbf{标题}`）。
  Must NOT do: 不得修改 lstlisting 内的代码内容；保留 `\begin{lstlisting}` 和 `\end{lstlisting}` 及其内部逐字内容不变。
  Parallelization: Wave 4 | Blocked by: 7 | Blocks: 9
  References: 行 532、566-568、571-586、636-646、648-657 等；`shellinbox` 和 `shell` 样式定义（行 78-89）
  Acceptance criteria: 原 infobox 代码块以独立 `\begin{lstlisting}[style=shell]` 形式存在；xelatex ×2 退出码 0。
  QA scenarios:
  - Happy: 代码块编译正确，样式一致（无额外边框），代码内容未改变。
  - Failure: `shellinbox` 改为 `shell` 后代码块边框异常 → 验证 lstlisting 设置中 `frame=single` 正常工作。
  Evidence: `.omo/evidence/reduce-boxes-pro/task-8-infobox-code/`
  Commit: Y | refactor(tex): extract infobox code blocks to standalone listings

- [x] 9. 转换剩余 infobox 为散文段落
  What to do: 扫描全文剩余 ~20 个 infobox（已排除任务 6/7/8 处理过的）。对每个：移除 `\begin{infobox}` 和 `\end{infobox}`；内部 `\textbf{标题}` 保留为段落首行粗体；列表结构保持；说明性文字转为普通正文段落。添加 `\medskip` 前后间距。
  Must NOT do: 不得修改 warnbox（务必确保 infobox 的 begin/end 移除不误伤 warnbox）；不得修改任何内容语义；不得引入新环境。
  Parallelization: Wave 4 | Blocked by: 8 | Blocks: 10
  References: 全文剩余 `\begin{infobox}` 行（排除任务 6/7/8 已处理的）；`\newtcolorbox{infobox}` 定义（行 111-118）
  Acceptance criteria: 全文 `\begin{infobox}` 为 0；xelatex ×2 退出码 0；第二次编译后 .log 中无 undefined reference 警告。
  QA scenarios:
  - Happy: 所有剩余 infobox 内容以散文段落形式自然呈现，无视觉断裂，无编译错误。
  - Failure: 某 infobox 的 begin/end 行意外移除了相邻 prose 的括号或环境 → `git diff` 逐行验证。
  Evidence: `.omo/evidence/reduce-boxes-pro/task-9-infobox-prose/`
  Commit: Y | refactor(tex): convert remaining infobox instances to prose paragraphs

- [x] 10. 增量编译验证与警告回归检查
  What to do: 在所有 ~89 个框转换完成后，运行最终两次 xelatex 编译。对比基线：统计 Overfull/Underfull 警告增量（预期 ≤ 5 条新增）。对比 .aux 中所有 `\newlabel{fig:...}` 和 `\newlabel{tab:...}` 的计数器值（应与基线完全一致）。验证 warnbox 计数仍为 8。
  Must NOT do: 不得在编译失败时强行提交；不得忽略新增 overfull 警告（超过 5 条需回溯排查）。
  Parallelization: Wave 5 | Blocked by: 9 | Blocks: F1-F4（并行）
  References: `.omo/evidence/reduce-boxes-pro/baseline/` 中的基线 `.log` 和 `.aux`
  Acceptance criteria: 编译退出码 0；新增 overfull/underfull ≤ 5；.aux 标签计数器零漂移；warnbox 计数 = 8。
  QA scenarios:
  - Happy: 所有指标通过，准备进入最终验证。
  - Failure: 新增 overfull 超过基线 → 分析 `.log`，回到对应任务查找引入行。
  Evidence: `.omo/evidence/reduce-boxes-pro/task-10-regression/`
  Commit: N（验证后任务，不计入 git）

- [x] F1. 最终验证：Box 移除完整性审计
  Verify: `Select-String -Pattern '\\begin\{(tipbox|keybox|stepbox|infobox)\}' Git_VSCode_Tutorial.tex` 各返回 0 匹配。`Select-String -Pattern '\\begin\{warnbox\}' Git_VSCode_Tutorial.tex` 返回 8 匹配。`git diff -- Git_VSCode_Tutorial.tex | Select-String 'warnbox'` 返回 0（无 warnbox 行差异）。
  Evidence: `.omo/evidence/reduce-boxes-pro/F1-box-audit.txt` — 保存 grep 输出。

- [x] F2. 最终验证：交叉引用完整性
  Verify: 第二次 xelatex 编译后 `.log` 无 `LaTeX Warning: Reference.*undefined`。提取 .aux 中所有 `\newlabel{...}` 条目，与基线 .aux 对比：计数器值（`{<counter>}` 段）全部一致。
  Evidence: `.omo/evidence/reduce-boxes-pro/F2-crossref.txt` — 保存对比输出。

- [x] F3. 最终验证：编译质量回归
  Verify: 对比基线 `.log` 和最终 `.log` 的 Overfull/Underfull 警告行。新增警告 ≤ 5 条，且无 "Emergency stop" 或 "Fatal error"。
  Evidence: `.omo/evidence/reduce-boxes-pro/F3-compile-quality.txt` — 保存 Overfull 对比。

- [x] F4. 最终验证：warnbox 逐字节完整性
  Verify: 用基线记录的 warnbox 内容 SHA-256 对比当前 .tex 中 warnbox 区块（从 `\begin{warnbox}` 到 `\end{warnbox}` 的逐行提取）。所有 8 个 SHA-256 必须一致。
  Evidence: `.omo/evidence/reduce-boxes-pro/F4-warnbox-integrity.txt` — 保存每个 warnbox 的 SHA-256 对比。

## Commit strategy
- 每任务独立提交（`refactor(tex): <任务描述>`）
- 格式：`<type>(<scope>): <短摘要>`，type = `refactor`，scope = `tex`
- 任务 1 和 10 为验证/备份任务，不提交
- 所有任务完成后用 `git log` 验证提交链完整

## Success criteria
1. `Git_VSCode_Tutorial.tex` 中仅存在 `warnbox` tcolorbox 环境（8 个），无 `tipbox`/`keybox`/`stepbox`/`infobox`
2. xelatex 编译两次零致命错误
3. 所有交叉引用（`\ref{fig:...}`、`\ref{tab:...}`）解析正确，无 `??`
4. 表号和图号与原文档一致
5. warnbox 8 个实例内容逐字节不变
6. 无新增 LaTeX 环境或宏包依赖
7. Overfull/Underfull 警告增量 ≤ 5 条
