# Learnings — table-styling-fix

Conventions, patterns, and successful approaches discovered during work on this plan.

_Auto-scaffolded by /start-work. Append new entries below - never overwrite._

---

## 2026-08-03 [Todo 1] 导言区新增 `\renewcommand{\tablename}{表}`
- 在 `Git_VSCode_Tutorial.tex` 行 135 `\renewcommand{\figurename}{图}` 之后插入 `\renewcommand{\tablename}{表}`（行 136），缩进一致（column 0）
- 编辑前 grep 确认全文无既有 `\renewcommand{\tablename}`，无重复定义
- 编辑后自检：figurename 行原样未动，上下文完整；文件行数 3149 → 3150
- 说明：caption 宏包已加载（行 26），table caption 将渲染为「表 N.M」；LSP 报的 Undefined reference 为 `\ref` 未编译的既有现象，两次编译后自动解析

## 2026-08-03 [Todo 2] 3 处 infobox 内表格改为 tabularx + \captionof
- 修复目标：plain tabular 的固定 `p{6cm}/p{5cm}` 列宽导致表格左对齐偏坠、右侧大片空白
- 转换模式（每处 2 个 edit：①标题行 ②`\begin{tabular}` 行 + `\end{tabular}`）：
  1. 1.1 节（行 425-435）：`\textbf{版本控制的发展简史}` → `\captionof{table}{版本控制的发展简史}\label{tab:vc-history}`；列型 `lllp{6cm}` → `l l l >{\raggedright\arraybackslash}X`
  2. 1.2 节（行 453-464）：标题尾冒号「：」随行删除 → `\captionof{table}{集中式 vs 分布式版本控制}\label{tab:centralized-vs-distributed}`；列型 `llp{5cm}p{5cm}` → `l >{\raggedright\arraybackslash}X >{\raggedright\arraybackslash}X`（3 列）
  3. 5.1 节（行 674-686）：标题「（初学者常混淆）：」随行删除 → `\captionof{table}{Git vs GitHub 的区别}\label{tab:git-vs-github}`；列型 `lp{6cm}p{6cm}` → `l >{\raggedright\arraybackslash}X >{\raggedright\arraybackslash}X`
- 通用要点：
  - 删除 no-op 垫片 `\arrayrulecolor{black}`（导言区行 30 已有 \providecommand 垫片，仅剩注释提及）
  - `\end{tabular}}` → `\end{tabularx}}`，局部组 `{...}` 外层保留
  - `\begin{infobox}[boxsep=2mm, left=6mm, right=6mm]` 行不动；数据行（\toprule 到 \bottomrule）逐字不动
  - caption 行不缩进（column 0），与 infobox 同行距保持原样（表 2 无空行、表 1/3 有空行，均已保留）
- 自检结果：`\captionof{table}` 恰好 3 处、3 个 label 各 1 处、`\begin{tabularx}{\linewidth}` 恰好 3 处、表格局部组内 `\arrayrulecolor{black}` 残留 0 处
- 文件行数保持 3150 不变（替换不增减行）
- 注意：编辑中间态 LSP 会报 "Mismatched environment"（`\begin{tabularx}` 与 `\end{tabular}` 不同步），全部 6 个 edit 完成后消失，属预期

## 2026-08-03 [Todo 3] 4 处独立 table 浮动表格改为 tabularx{\textwidth} + \caption + \label
- 修复目标：plain tabular 固定 `p{6cm}/p{7cm}/p{5cm}` 列宽导致列宽窄于 textwidth、左侧偏坠、右侧空白
- 转换模式（每处 2 个 edit：①caption+label 插入 ②`\begin{tabular}` 行改列型 + `\end{tabular}` 改 `\end{tabularx}`）：
  1. 2.2 节配置层级（行 939-951）：caption `Git 配置的三个层级`，label `tab:config-levels`；列型 `llp{6cm}` → `l l >{\raggedright\arraybackslash}X`
  2. 3.1 节文件状态（行 1187-1200）：caption `Git 中文件的四种状态`，label `tab:file-states`；列型 `llp{7cm}` → `l l >{\raggedright\arraybackslash}X`
  3. 4.2 节分支命名规范（行 1718-1731）：caption `常见分支命名规范`，label `tab:branch-naming`；列型 `llp{5cm}` → `l l >{\raggedright\arraybackslash}X`
  4. 6.10 节推荐扩展（行 2443-2458）：**已是 tabularx{\textwidth}{l l X} 撑满**，仅插入 caption `推荐 VS Code 扩展` + label `tab:recommended-extensions` 于 `\centering` 后、`\renewcommand{\arraystretch}{1.5}` 前；列型 `l l X` 保持不动（X 列已正常，不加 raggedright）
- 通用要点：
  - caption+label 放于 `\centering` 之后、`\begin{tabularx}` 之前（新增 1 行）；数据行（\toprule 到 \bottomrule）逐字不动
  - `\end{tabular}` → `\end{tabularx}` 时外层 `\end{table}` 保持；表格 D 不改 `\end{tabularx}`
  - caption 中文无「Table」字样（导言区已有 \tablename=表）
  - `\end{tabular}` 匹配须带上文独特数据行消歧（`\end{tabular}` 全文有 3 处，不能裸匹配）
- 自检结果：4 个 label 各 1 处、4 处 `\caption{` 均位于 `\centering` 后、全文无残留 plain `\begin{tabular}`/`\end{tabular}`、表格 D 列型 `l l X` 未动
- 文件行数 3150 → 3154（每处 +1 行 caption+label）
- 中间态 LSP "Mismatched environment" 随 7 个 edit 全部完成后消失；"Undefined reference" 为既有现象（Todo 5 插入 ref 后两次编译解析）

## 2026-08-03 [Todo 6] AGENTS.md 行 55 表格样式规范改为 tabularx 撑满内宽
- 修订 `C:\Users\Admin\Desktop\Git\AGENTS.md` 行 55（「框内代码与表格样式约定」小节），使文档规范与 .tex 实际写法（Todo 2）一致
- 变更内容：plain tabular + `\arrayrulecolor{black}` → `\begin{tabularx}{\linewidth}{...}` 撑满内宽 + 局部组内 tabularx；追加 `\captionof{table}{标题}\label{tab:xxx}` 要求；保留「禁用 tcolorbox 的 `tabularx` 键」警告并注明 tabularx 宏包环境本身不受限
- 替换模式：`\begin{tabular}...\end{tabular}` → `\begin{tabularx}{\linewidth}{...}...\end{tabularx}`
- 自检：grep「禁用 tcolorbox 的 `tabularx` 键」与「tabularx 宏包环境本身不受限」均命中行 55；其余行零改动

## 2026-08-03 [Todo 4] 7 处 longtable 加 \caption + \label
- 修复目标：longtable 无标题、表号，无法被正文 \ref 引用
- 插入模式：`\begin{longtable}{...}` 行后紧跟首行插入 `\caption{标题}\label{tab:xxx}\\`（以 `\\` 结尾，longtable 必需），其后 `\toprule` 及所有块原样
- 7 处明细（标题/label）：新手避坑速查表 tab:pitfalls（8.1，4 列）；常见报错与一键修复 tab:common-errors（8.3，2 列）；附录基础/分支/远程/撤销操作 tab:cmd-basic/branch/remote/undo（4 处同列规格 `p{5.5cm} p{9cm}`）；VS Code 快捷键 tab:vscode-shortcuts（3 列）
- 消歧要点：4 处附录表列规格完全相同，edit 的 oldString 必须带上行 `\section{基础操作}` 等 + 空行 + begin 行，否则 "multiple matches" 失败；列规格唯一的表（tab:pitfalls/common-errors/vscode-shortcuts）可直接匹配 `\begin{longtable}{...}` + `\toprule`
- 通用要点：caption 后必须有 `\\`；表号只在首页显示，跨页由 endhead 表头承担；列规格/数据行/endfirsthead/endhead/endfoot/endlastfoot 全部不动
- 自检：7 个 label 各 1 处、表格类 `^\caption{...}\label{tab:` 共 11 处（4 独立 table + 7 longtable）、每处 caption 行号 = begin 行号 + 1
- LSP "Undefined reference" 为既有现象，两次编译后解析（归 Todo 7）；行数 3154 → 3161（每处 +1 行）

## 2026-08-03 [Todo 5] 正文插入 8 处 `\ref{tab:xxx}` 引用
- 8 处插入点全部以 grep 引导句定位（行号已漂移），逐字按插入点表执行；`\ref` 统一用 `表~\ref{tab:xxx}` 句式（`~` 非断行空格），与既有 `图~\ref{fig:xxx}` 体系一致
- 三种插入形态：
  1. **独立段落/独立句（前后留空行）**：tab:vc-history（infobox 前）、tab:branch-naming（\subsection 与 \begin{table} 之间）
  2. **句号后接新句（同段）**：tab:centralized-vs-distributed、tab:git-vs-github、tab:pitfalls（`完整对照见表~...。`）
  3. **冒号后补括号/逗号从句**：tab:config-levels（`：（见表~...）`）、tab:file-states（`：，如表~... 所示`）、tab:recommended-extensions（`：（见表~...）`）
- 自检：`\ref{tab:` 恰好 8 处、8 个 label 各引用 1 次；表格环境（\caption/\label/列型/数据行）零改动；文件行数 3161 → 3165（2 处独立段落各净增 2 行，6 处行内追加不增行）
- 明确不给 tab:common-errors 及附录 6 个 longtable（tab:cmd-*、tab:vscode-shortcuts）造 ref（无引导句，仅留 label 供将来引用）
- LSP "Undefined reference" 为未编译的既有噪音，归 Todo 7 两次编译后自动解析

## 2026-08-03 [Todo 5 follow-up] 行 1187 标点修正「：，」→「：…所示。」
- 原计划规格「句末补『，如表~\ref{tab:file-states} 所示』」有缺陷：原句以「：」结尾（`…的基础：`），补「，」会造成「：，」连用，不符合中文标点规范
- 修正：删除冒号后逗号，句末补句号闭合 → `…掌握 Git 的基础：如表~\ref{tab:file-states} 所示。`
- 自检：grep「：如表~\ref{tab:file-states} 所示。」唯一命中行 1187；「基础：，」无残留；其余行零改动；文件行数保持 3165
- LSP 报 "Undefined reference" 为既有噪音，归 Todo 7；未运行 xelatex、未执行 git 操作

## 2026-08-03 [Todo 7] 两次 xelatex 编译验证（唯一验证门）— PASS
- 编译前确认无残留 xelatex 进程（Get-Process 为空），无需 taskkill
- 命令（-interaction=nonstopmode，目录 C:\Users\Admin\Desktop\Git）：
  1. 第一次：EXITCODE=0，输出 `Git_VSCode_Tutorial.pdf (87 pages)`；日志含 `LaTeX Warning: There were undefined references.` 与 `Label(s) may have changed. Rerun to get cross-references right.`（首次编译预期，第二次解析）
  2. 第二次：EXITCODE=0，同样 87 页；**无 undefined references / Rerun 警告残留**
- Reference 检查（第二次编译后 .log）：
  - grep `Reference.*undefined|undefined references|LaTeX Warning: Reference` → 0 命中
  - .aux 中 `\newlabel{tab:` 共 14 处，8 个必查 ref 全部解析为「表 N.M」：
    tab:vc-history→1.1（12 页）、tab:centralized-vs-distributed→1.2（13）、tab:git-vs-github→1.3（18）、tab:config-levels→2.1（25）、tab:file-states→3.1（33）、tab:branch-naming→4.1（48）、tab:recommended-extensions→6.1（70）、tab:pitfalls→8.1（78）
- 错误与警告：grep `^!`（LaTeX Error / Package Error 行）→ 0 命中；Overfull \hbox 仅 6 处（Underfull 亦有零星，均允许范围，无爆炸）
- 产物：`Git_VSCode_Tutorial.pdf` 3065521 字节，LastWriteTime 2026-08-03 11:33:34（第二次编译时刻），87 页
- 结论：8 个 `表~\ref{tab:xxx}` 交叉引用 + 14 处表格 caption（表 N.M 编号）全部正确渲染；编译验证 PASS，可进入 Final wave F1-F4
- 本任务零源码改动，未执行 git 操作，未打开 PDF 视觉检查

## 2026-08-03 [F4] 范围保真审计（对照 Scope IN/OUT）— VERDICT: APPROVE
- **审计方法**：`git status`/`git diff --stat`/`git diff -- Git_VSCode_Tutorial.tex`（全文 112+/81-）逐 hunk 分类 + `git diff -- AGENTS.md` + `git status --porcelain`；对照 `.omo/plans/table-styling-fix.md` Scope IN/OUT
- **本计划改动逐项核对（全部符合 IN）**：
  - IN-1 导言 tablename：`\renewcommand{\tablename}{表}` 位于 figurename 之后（行 136），figurename 行未动
  - IN-2 14 处表格：3 captionof（vc-history/centralized-vs-distributed/git-vs-github）+ 4 caption（config-levels/file-states/branch-naming/recommended-extensions）+ 7 longtable caption（pitfalls/common-errors/cmd-basic/branch/remote/undo/vscode-shortcuts）；label 各 1 处；`.aux` 中 `\newlabel{tab:` 恰好 14 个全部落盘
  - IN-3 8 处 ref：全部按插入点表执行（tab:vc-history/branch-naming 为独立句，其余 6 处句末/行内追加），句式「表~\ref{}」与图体系一致
  - IN-4 AGENTS.md 行 55：与 Todo 6 规格逐字一致（tabularx{\linewidth} 撑满 + 局部组 + captionof + 保留禁用 tcolorbox 的 tabularx 键）
  - IN-5 两次编译：learnings Todo 7 已记录 exit 0×2、87 页、无 undefined ref
  - 数据行（\toprule→\bottomrule）逐字未动；列规格转换符合总纲（l 保留、末列 X+raggedright、X≤2）；longtable 列规格未动、caption 以 \\ 结尾；附录 6 处 longtable 仅留 label 未造 ref
- **Scope OUT 零违规**：章节结构零增删；tcolorbox 环境定义（行 86-129）零改动；sc-*.md/images/ 零改动（git diff --name-only 为空）；\usepackage 零新增（arrayrulecolor 是 providecommand 垫片非宏包）；fig:* label/ref 名称零改动；figurename 行未动；无 git 写操作
- **关键发现：工作区 diff 含其他两个计划的改动**（git diff 相对 HEAD，是 box-content-polish → fix-tikz-diagrams → table-styling-fix 三计划累积）：
  - fix-tikz-diagrams：~12 处 TikZ hunk（resizebox 包裹、text width、坐标调整、legend text width=14cm）——见其 plan Must have
  - box-content-polish：`\lstdefinestyle{shellinbox}` + 10 处 `[style=shellinbox]`、`\providecommand{\arrayrulecolor}` 垫片、AGENTS.md「框内代码与表格样式约定」/「编译环境陷阱」小节、安全边界「环境清理」行——见其 plan Must have
  - 3 处 infobox 的 `boxsep=2mm, left=6mm, right=6mm` 参数行改动归属 box-content-polish（其 Must have 明确含「infobox 设 boxsep/left/right 留白」；本计划执行时该行已带参数，learnings Todo 2 记录「行不动」）
  - AGENTS.md 行 18/84 空格微调（`\ref`/`\label`、`\vscodeimg`/ 后空格）、`.omo/boulder.json` 状态切换（Boulder 工作流自动维护）
  - 上述均非本计划超范围；三个计划各自 Scope 有对应条目
- **untracked 检查**：全部位于 .omo/ 下（drafts/evidence/notepads/plans/run-continuation/start-work），无意外源码文件；编译产物 .aux/.log/.pdf/.toc 为 tracked 文件（仓库历史已跟踪），显示 modified 属预期
- **结论**：table-styling-fix 计划实际改动 100% 落在 Scope IN，Scope OUT 零侵入，无超范围改动 → **F4 VERDICT: APPROVE**（提交前需注意工作区含三计划累积改动，是否一并提交由用户决定）

## 2026-08-03 [F1] 计划合规审计（Final Verification Wave 1）— VERDICT: APPROVE
- 只读审查（grep + Read 定位，未改 .tex/AGENTS.md/sc-*.md/images，未编译，未执行 git 写操作）
- **子项 A：14 处表格全部改造 — PASS**
  - 3 infobox：行 427 `\captionof{table}`+tab:vc-history（infobox 426，tabularx{\linewidth} 429）；行 455 tab:centralized-vs-distributed（454/456）；行 676 tab:git-vs-github（675/678）——三处均 Read 亲验在 infobox 内、列型 `l >{\raggedright\arraybackslash}X` 撑满
  - 4 独立 table：行 943 tab:config-levels（table 941，tabularx{\textwidth} 944）；1191 tab:file-states（1189/1192）；1724 tab:branch-naming（1722/1725）；2449 tab:recommended-extensions（2447/2451，列型 `l l X` 保持未动，符合 Todo 3 要求）
  - 7 longtable：caption 行 = begin 行 + 1 全部成立——2731/2730、2778/2777、3024/3023、3051/3050、3073/3072、3095/3094、3117/3116（tab:pitfalls/common-errors/cmd-basic/cmd-branch/cmd-remote/cmd-undo/vscode-shortcuts）
  - 计数核对：`\label{tab:` 恰好 14；表格 caption = 3 captionof{table} + 11 \caption = 14；无 plain `\begin{tabular}` 残留；tabularx 7 处（3×{\linewidth} + 4×{\textwidth}）
- **子项 B：8 处 `\ref{tab:` 齐全 — PASS**
  - 行 424/452/673/939/1187/1720/2445/2726 引用 tab:vc-history/centralized-vs-distributed/git-vs-github/config-levels/file-states/branch-naming/recommended-extensions/pitfalls，与计划插入点表完全一致
  - 行 1187 为「：如表~…所示。」（F5 follow-up 标点修正已落地）；common-errors 及附录 6 处 longtable 确认无 ref（符合「不造引用」决策）
- **子项 C：Scope OUT 零侵入 — PASS**
  - 章节结构：9 个 \chapter（第 1-8 章+附录）+ 第 0 章 3 section（253/316/352），10 章+附录划分未动
  - tcolorbox 环境定义区（行 94-134）：tipbox/warnbox/infobox/keybox/stepbox 图标/标签/颜色原样；figurename 行 135 未动，tablename 行 136 为唯一新增（Scope IN）
  - sc-*.md：git status 无条目；images/：27 文件无 git 变更，\vscodeimg 26 处引用路径全部存在
  - 37 图体系：26 位图（\vscodeimg 变体 26 调用）+ 11 TikZ（\label{fig: 直写 11）= 37 label 不变；ref 38 实例（modified-files 双引用）全覆盖无遗漏
  - 宏包 19 个 \usepackage 与基线一致，无新增；`\arrayrulecolor{black}` 实码残留 0（仅行 30 注释）；无 git 提交（HEAD 仍为 1e5cd59）
- 与计划 Scope IN/OUT 逐项核对通过；Todo 1-7 全部完成。F1 = APPROVE，可进入 F2-F4

## 2026-08-03 [Final F2] 代码质量审查（只读）— VERDICT: APPROVE
- 子项 A（LaTeX 语法）PASS：plain `\begin{tabular}` 0 命中；tabularx 7 begin/7 end 逐一配对（429↔437, 456↔466, 678↔688, 944↔952, 1192↔1201, 1725↔1734, 2451↔2461）；longtable 7 begin/7 end 逐一配对（2730↔2753, 2777↔2793, 3023↔3046, 3050↔3068, 3072↔3090, 3094↔3112, 3116↔3131）；longtable 的 caption+`\\`→toprule→endfirsthead/endhead 结构完整
- 子项 B（宏包合规）PASS：`\usepackage` 共 19 处（行 8-27），全部既有宏包，无新增；booktabs(12)/longtable(13)/array(14)/tcolorbox(16)/tabularx(23)/float(25)/caption(26) 行号与 AGENTS.md「导言区行 12-27」记载一致
- 子项 C（arrayrulecolor 残留）PASS：全文仅 3 处命中，均在导言区行 29-31（2 注释 + `\providecommand` 垫片本身）；表格局部组内调用 0 处
- 子项 D（样式一致性）PASS：3 处 infobox 保留 `\begin{infobox}[boxsep=2mm, left=6mm, right=6mm]`（行 426/454/675），内含 `\begin{tabularx}{\linewidth}` 局部组 + `\captionof{table}{中文}\label{tab:xxx}`；4 处独立 table 为 `\centering`→`\caption`→`\begin{tabularx}{\textwidth}{l l >{\raggedright\arraybackslash}X}`（表 D 行 2451 `l l X` 无 raggedright 属允许豁免）；7 处 longtable 均为 begin+1 行 `\caption{中文}\label{tab:xxx}\\`；tcolorbox `tabularx=` 键 0 命中；14 个 `\label{tab:` 与 Todo 7 编译验证的 14 个 `\newlabel{tab:` 完全对应
- 审查方式：grep 全量 + 逐区域 Read 抽查（3 infobox / 4 独立 table / 2 longtable 代表），未运行 xelatex、未做任何写操作


## 2026-08-03 [F3] 真实手动 QA（Final Verification Wave）— APPROVE
- 方法：pdfplumber 文本层全量扫描 + pdfplumber 渲染 11 页 PNG（resolution=110）+ look_at 视觉核对；未修改任何文件、未编译、未 git 操作
- 14 处表格页码定位（.aux 页码 = PDF 物理页，全部命中）：表1.1→p12、1.2→p13、1.3→p18、2.1→p25、3.1→p33、4.1→p48、6.1→p70、8.1→p78、8.2→p80、9.1→p84、9.2/9.3/9.4→p85、9.5→p86
- ①表号前缀：14/14 渲染为「表 N.M: 标题」，无 Table 残留
- ②宽度：infobox 3 处（1.1/1.2/1.3）表格与框边齐平、无右侧空白（视觉确认）；独立 table 4 处（2.1/3.1/4.1/6.1）撑满 textwidth、居中；longtable 7 处撑满页面宽度
- ③内容：14/14 完整（关键数据行均在文本层可见）
- ④跨页 longtable：仅 2 处实际跨页——表 8.1（p78→79，续页重复 4 列表头「场景/错误做法/正确做法/后果对比」、无表号重复）；表 9.4（p85→86，续页重复「命令 说明」表头）。其余 5 处（8.2/9.1/9.2/9.3/9.5）单页完整
- 8 处正文 ref 全部解析正确（无 ??）：p12 表1.1、p13 表1.2、p18 表1.3、p25 表2.1、p32 表3.1（ref 在 32 页、caption 在 33 页，跨页引用正常）、p48 表4.1、p70 表6.1、p78 表8.1
- 视觉确认 12 页（12/13/18/25/33/48/70/78/79/80/84/86），超过 4-6 页最低要求
- 结论：14/14 表格 + 8/8 ref 全部通过 → **VERDICT: APPROVE**
