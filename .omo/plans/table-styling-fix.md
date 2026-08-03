# table-styling-fix - Work Plan

## TL;DR (For humans)
- **方案**：修复 `Git_VSCode_Tutorial.tex` 全文档 14 处表格排版。1.1 节「版本控制的发展简史」等 3 处 infobox 内表格的「难看」根因是 plain tabular 未居中、列宽窄于 infobox 内宽导致右侧大片空白；统一改为 `tabularx` 撑满内宽/textwidth，并为全部 14 处表格（3 infobox + 4 独立 table + 7 longtable）加「表 N.M」编号体系（`\caption`/`\captionof`+`\label`+正文 `\ref`），与既有 37 图引用体系统一；导言区补 `\renewcommand{\tablename}{表}` 让表号中文化；AGENTS.md 行 55 相应修订一行。
- **收益**：表格不再偏坠留白；14 处表格全部可交叉引用；表号渲染为「表 N.M」而非「Table N.M」。
- **不做**：不改章节结构、tcolorbox 环境定义、`sc-*.md`、`images/`、37 图体系；不引新宏包；不自动提交 git。
- **工作量**：1 个 .tex 文件（导言 1 行 + 14 处表格 + 8 处正文引用）+ AGENTS.md 修订 1 行；7 个实现 todo + 4 项最终验证；两次 xelatex 编译为唯一验证门。
- **风险**：tabularx 窄列 CJK overfull（已用 `>{\raggedright\arraybackslash}X` 缓解）；`\captionof` 在 tcolorbox 内需首次编译实测；longtable 表号跨页行为需核对。
- **决策**：① infobox 内表格宽度 → 用户选定「允许 tabularx 撑满内宽」（修订 AGENTS.md）；② 编号体系范围 → 用户选定「全部 14 处表格加编号」。

## Scope

### IN
1. `Git_VSCode_Tutorial.tex` 导言区：行 135 `\renewcommand{\figurename}{图}` 之后补 `\renewcommand{\tablename}{表}`（表号中文化，渲染为「表 N.M」）
2. `Git_VSCode_Tutorial.tex` 正文 **14 处表格**改造（3 infobox 内 + 4 独立 table + 7 longtable），清单与目标形态见 Execution strategy
3. 正文 **8 处**补 `\ref{tab:xxx}` 引用（见 Execution strategy 的 ref 插入点表）
4. `AGENTS.md` 行 55 表格样式约定修订：放宽为「infobox 内用 tabularx 宏包环境 `\begin{tabularx}{\linewidth}{...}` 撑满内宽」，保留「禁用 tcolorbox 的 tabularx 键」
5. 两次 xelatex 编译验证 + PDF 视觉抽查

### OUT (Must-NOT-Have)
- 不新增/删除章节结构，不改 10 章 + 附录的章节划分
- 不修改 tcolorbox 环境定义（tipbox/warnbox/infobox/keybox/stepbox 的图标/标签/颜色/边框）
- 不修改 `sc-*.md` 参考文档（只读）
- 不修改 `images/` 目录任何文件
- 不引入新宏包（caption/tabularx/longtable/booktabs/array 均已加载于行 12-27）
- 不改动既有 37 图引用体系（fig:* 的 label/ref 全部保持原样）
- 不新增 tcolorbox 环境类型，不放宽「不得新增环境类型」约束
- 不自动提交 git（AGENTS.md 安全边界：Never 自动提交）
- 不删除任何表格行/列内容，仅改环境、列规格、加 caption/label/ref
- 不修改 `\renewcommand{\figurename}{图}` 行本身（仅在其后追加 tablename 行）

## Verification strategy
- 编译命令（AGENTS.md 构建命令，必须连续两次）：
  ```
  C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex
  C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex
  ```
- 通过标准：两次编译均 exit 0、无 `!` 致命错误；允许 Overfull/Underfull 警告；第二次编译日志无 `LaTeX Warning: Reference ... undefined` 残留（除文档正文刻意出现的 Git 报错示例文本 "fatal: refusing to" 外）
- 编译环境陷阱（AGENTS.md）：残留 xelatex 进程锁 `.log/.aux/.toc` 时先 `taskkill /f /im xelatex.exe`；编译失败时删除 `.aux/.toc/.out/.log` 后重试
- LSP 已知噪音：`.aux` 未生成前 `Undefined reference` 报错为预期，两次编译后消失；不作为失败判据
- 视觉验证：PDF 中 14 处表格逐一核对（居中、撑满、表号「表 N.M」、ref 解析正确）

## Execution strategy

### 改造总纲
- **定位方式**：执行者以 grep（`\begin{tabular}` / `\begin{table}` / `\begin{longtable}` / `\begin{tabularx}`）定位，不依赖绝对行号（前面 todo 改动会使行号漂移）。下列 References 行号为 2026-08-03 基线快照，仅作核对；每次改动后行号会漂移，一律以 grep 定位为准。
- **所有表格**：`\caption`/`\captionof` + `\label{tab:xxx}`；正文有引导句处补 `\ref{tab:xxx}`。
- **tabularx 列规格规则**：原 plain tabular 的 `l` 列保留 `l`，原 `p{Xcm}` 末列改为 `X`（撑满剩余宽度）；多列时按「信息列保留 l、说明列改 X」原则，X 至多 2 个（多个说明列时后者用 `>{\raggedright\arraybackslash}X` 防断行溢出）。

### Todo 1：导言区中文化表号
- **位置**：`Git_VSCode_Tutorial.tex` 行 135 `\renewcommand{\figurename}{图}` 之后
- **动作**：追加一行 `\renewcommand{\tablename}{表}`
- **References**: `Git_VSCode_Tutorial.tex:135`
- **Acceptance**: 编译后表格 caption 渲染为「表 N.M：标题」而非「Table N.M」
- **QA happy**: 编译 PDF 中任一处表格 caption 前缀为「表」
- **QA failure**: caption 仍显示「Table」→ 检查 tablename 定义是否在 `\begin{document}` 前
- **Commit**: 不自动提交（AGENTS.md 禁自动 git）

### Todo 2：infobox 内 3 处表格改造（tabularx 撑满内宽 + captionof 编号）
- **位置**：`Git_VSCode_Tutorial.tex` 行 426（1.1 节版本控制发展简史）、行 453（1.2 节集中式 vs 分布式）、行 675（5.1 节 Git vs GitHub）
- **动作**（每处）：
  - 原 `{\renewcommand{\arraystretch}{1.3}\arrayrulecolor{black}\setlength{\tabcolsep}{6pt}\begin{tabular}{...}...\end{tabular}}`
  - 改为 `{\renewcommand{\arraystretch}{1.3}\setlength{\tabcolsep}{6pt}\begin{tabularx}{\linewidth}{...}...\end{tabularx}}`（删除 no-op 垫片 `\arrayrulecolor{black}`；tabularx 撑满 infobox 内宽）
  - **保留 `\begin{infobox}[boxsep=2mm, left=6mm, right=6mm]` 行不变**，仅改框内表格环境与标题行
  - 原 `\textbf{标题}` 行改为 `\captionof{table}{标题}\label{tab:xxx}`（caption 宏包已加载于行 26，支持非浮动环境 caption；原粗体标题内容移入 caption，删除原 `\textbf{...}` 行）
- **列规格**：
  - 行 426 `lllp{6cm}` → `{l l l >{\raggedright\arraybackslash}X}`（特点列撑满；X 加 raggedright 防 CJK overfull）
  - 行 453 `llp{5cm}p{5cm}` → `{l >{\raggedright\arraybackslash}X >{\raggedright\arraybackslash}X}`（集中式/分布式两列均衡撑满）
  - 行 675 `lp{6cm}p{6cm}` → `{l >{\raggedright\arraybackslash}X >{\raggedright\arraybackslash}X}`（Git/GitHub 两列均衡撑满）
- **label 命名**：`tab:vc-history`、`tab:centralized-vs-distributed`、`tab:git-vs-github`
- **References**: `Git_VSCode_Tutorial.tex:423-435`、`:451-464`、`:672-686`；AGENTS.md 行 55（修订后）；导言区行 26（caption）
- **Acceptance**: 3 处表格均撑满 infobox 内宽（左右无大片空白）、有表号「表 1.x / 表 5.x」、原 `\textbf{...}` 标题行已删除且内容移入 `\captionof`
- **QA happy**: PDF 中 3 处 infobox 内表格宽度与框宽基本齐平、表号正确
- **QA failure**: 编译报 `\captionof` undefined → 确认 caption 宏包已加载（行 26）；表格仍窄 → 确认 `\linewidth` 而非 `\textwidth`
- **Commit**: 不自动提交

### Todo 3：独立 table 4 处改造（tabularx 撑满 textwidth + caption+label）
- **位置**：`Git_VSCode_Tutorial.tex` 行 938（2.2 配置层级）、行 1185（3.1 文件状态）、行 1715（4.2 分支命名规范）、行 2439（6.10 推荐扩展）
- **动作**（每处）：
  - 行 938（`llp{6cm}`）/ 行 1185（`llp{7cm}`，实际位于 1187）/ 行 1715（`llp{5cm}`）：`\begin{tabular}{llp{Xcm}}` → `\begin{tabularx}{\textwidth}{l l >{\raggedright\arraybackslash}X}`；保留 `\centering`（以 grep `\begin{tabular}` 定位各表实际列规格，勿按行号盲改）
  - 行 2439：已是 `\begin{tabularx}{\textwidth}{l l X}`，仅加 caption+label
  - `\caption{标题}\label{tab:xxx}` 放在 `\centering` 之后、表格环境之前
- **label 命名**：`tab:config-levels`、`tab:file-states`、`tab:branch-naming`、`tab:recommended-extensions`
- **References**: `Git_VSCode_Tutorial.tex:938-949`、`:1185-1197`、`:1715-1727`、`:2439-2453`
- **Acceptance**: 4 处表格撑满 textwidth、有表号；行 2439 仅加 caption+label 无列改动
- **QA happy**: PDF 中 4 处表格宽度接近 textwidth、表号正确、`\ref` 解析正确
- **QA failure**: tabularx 与 booktabs 共存报错（`\toprule` 内）→ 确认未使用 tcolorbox 的 tabularx 键（TeX.SE 380361）；列宽溢出 → X 列前加 `>{\raggedright\arraybackslash}`
- **Commit**: 不自动提交

### Todo 4：longtable 7 处加 caption+label
- **位置**：`Git_VSCode_Tutorial.tex` 行 2721（8.1 核心避坑表）、行 2767（8.3 常见报错）、行 3012（附录基础操作）、行 3038（附录分支操作）、行 3059（附录远程操作）、行 3080（附录撤销操作）、行 3101（附录 VS Code 快捷键）
- **动作**（每处）：`\begin{longtable}{...}` 之后、`\toprule`（endfirsthead 部分）之前插入：
  ```
  \caption{标题}\label{tab:xxx}\\
  ```
  （表号只在首页显示，跨页续行不重复，由 endhead 表头承担）
- **已验证**：7 处 longtable 均含 `\endfirsthead`/`\endhead` 块（行 2721/2767/3012/3038/3059/3080/3101 的 `\toprule...\endfirsthead` 结构已确认存在），caption 置于 `\begin{longtable}` 后首行、`\toprule` 之前、以 `\\` 结尾，是标准 longtable 用法
- **示例**（行 2767 常见报错）：
  before:
  ```
  \begin{longtable}{p{4cm} p{10cm}}
  \toprule
  \textbf{报错信息} & \textbf{原因与修复} \\
  \midrule
  \endfirsthead
  ```
  after:
  ```
  \begin{longtable}{p{4cm} p{10cm}}
  \caption{常见报错与一键修复}\label{tab:common-errors}\\
  \toprule
  \textbf{报错信息} & \textbf{原因与修复} \\
  \midrule
  \endfirsthead
  ```
- **label 命名**：`tab:pitfalls`、`tab:common-errors`、`tab:cmd-basic`、`tab:cmd-branch`、`tab:cmd-remote`、`tab:cmd-undo`、`tab:vscode-shortcuts`
- **列规格**：全部保持现状（`p{Xcm}` 组合，总和已接近 textwidth，不改）
- **References**: `Git_VSCode_Tutorial.tex:2721-2743`、`:2767-2782`、`:3012-3034`、`:3038-3055`、`:3059-3076`、`:3080-3097`、`:3101-3115`
- **Acceptance**: 7 处 longtable 均有表号；跨页表格（如行 2721 避坑表）首页显示表号、续页仅表头
- **QA happy**: PDF 中 7 处 longtable 表号正确、跨页行为正常（表头在续页重复、表号不重复）
- **QA failure**: longtable 内 `\caption` 报错 → 确认 caption 位于 `\begin{longtable}` 后第一行且以 `\\` 结尾
- **Commit**: 不自动提交

### Todo 5：正文 8 处补 `\ref{tab:xxx}` 引用
- **动作**：在下列表格的引导句处补引用（附录 6 处 longtable 前无正文引导句，不强行造引用，仅保留 label 供将来引用）
- **依赖**：必须在 Todo 2/3/4 完成后执行（全部 `\label{tab:xxx}` 已就位），否则 `\ref` 指向不存在的 label；执行顺序按 Todos 清单 1→7
- **插入点表**：

| label | 位置 | 引导句 | 插入方式 |
|-------|------|--------|----------|
| tab:vc-history | 行 423 infobox 前 | 无现成引导句 | 在 infobox 前插入独立句：「版本控制的发展简史见表~\ref{tab:vc-history}。」 |
| tab:centralized-vs-distributed | 行 450 末尾 | 「……这也解释了它为何如此强调速度、分布式与数据完整性。」 | 句末补「集中式与分布式的对比如表~\ref{tab:centralized-vs-distributed} 所示：」 |
| tab:git-vs-github | 行 670 末尾 | 「它为 Git 仓库提供云端托管服务。」 | 句末补「Git 与 GitHub 的对比如表~\ref{tab:git-vs-github} 所示：」 |
| tab:config-levels | 行 936 | 「Git 配置有三个层级，优先级从高到低：」 | 句末补「（见表~\ref{tab:config-levels}）」 |
| tab:file-states | 行 1183 | 「Git 中的文件有四种状态，理解这些状态是掌握 Git 的基础：」 | 句末补「，如表~\ref{tab:file-states} 所示」 |
| tab:branch-naming | 行 1713 subsection 后 | 无现成引导句 | 在 `\begin{table}` 前插入独立句：「常见的分支命名规范如表~\ref{tab:branch-naming} 所示：」 |
| tab:recommended-extensions | 行 2437 | 「以下扩展可以显著增强 VS Code 中的 Git 体验：」 | 句末补「（见表~\ref{tab:recommended-extensions}）」 |
| tab:pitfalls | 行 2717 | 「……遇到对应场景再回来查阅细节。」 | 句末补「完整对照见表~\ref{tab:pitfalls}。」 |

- **References**: 上表各行号对应正文
- **Acceptance**: 8 处 `\ref{tab:xxx}` 全部解析为正确表号（两次编译后无 undefined reference）；未对附录 6 处 longtable 造引用
- **QA happy**: grep 确认 8 处 `\ref{tab:` 存在；PDF 中引用文字显示正确表号
- **QA failure**: 引用显示 `??` → 对应 label 未定义（拼写核对 label 清单）或 .aux 未更新（再编译一次）
- **Commit**: 不自动提交

### Todo 6：AGENTS.md 行 55 规范修订
- **动作**：将行 55 从：
  「**表格进 infobox**：用 plain tabular + 局部组 `{\renewcommand{\arraystretch}{1.3}\arrayrulecolor{black}\setlength{\tabcolsep}{6pt}\begin{tabular}...\end{tabular}}`；infobox 设 `boxsep=2mm, left=6mm, right=6mm` 撑满框宽。**禁用 tcolorbox 的 `tabularx` 键**（会劫持 booktabs 规则色，见 TeX.SE 380361）」
  改为：
  「**表格进 infobox**：用 `\begin{tabularx}{\linewidth}{...}` 撑满内宽 + 局部组 `{\renewcommand{\arraystretch}{1.3}\setlength{\tabcolsep}{6pt}\begin{tabularx}{\linewidth}{...}...\end{tabularx}}`；infobox 设 `boxsep=2mm, left=6mm, right=6mm` 撑满框宽；表格加 `\captionof{table}{标题}\label{tab:xxx}`。**禁用 tcolorbox 的 `tabularx` 键**（会劫持 booktabs 规则色，见 TeX.SE 380361；tabularx 宏包环境本身不受限）」
- **References**: `AGENTS.md:55`；本计划 Execution strategy 总纲
- **Acceptance**: AGENTS.md 行 55 与 `.tex` 中实际写法规约一致；未误删「禁用 tcolorbox 的 tabularx 键」警告
- **QA happy**: 通读修订后行 55，描述与 3 处 infobox 表格实码吻合
- **QA failure**: 规范与实码仍不一致 → 逐字对照修订
- **Commit**: 不自动提交

### Todo 7：两次 xelatex 编译 + PDF 视觉检查
- **动作**：执行构建命令两次；清理残留进程（如需要）；打开 PDF 抽查 14 处表格
- **References**: AGENTS.md「构建命令」「编译环境陷阱」；本计划 Verification strategy
- **Acceptance**: 两次编译 exit 0 无致命错误；第二次编译无 `Reference ... undefined` 警告；14 处表格均：居中或撑满、有表号「表 N.M」、内容未丢失
- **QA happy**: 编译日志无 `! ` 行；第二次编译后 grep `.log` 无 `Reference ... undefined` 残留；`Overfull` 警告数相对基线不显著增加（X 列相关）；PDF 逐处核对 14 处表格
- **QA failure**: 编译 exit≠0 → 读 `.log` 定位、删除 `.aux/.toc/.out/.log` 重试、必要时 `taskkill /f /im xelatex.exe`
- **Commit**: 不自动提交

## Todos
- [x] 1. 导言区补 `\renewcommand{\tablename}{表}`（行 135 figurename 之后）——表号渲染为「表 N.M」
- [x] 2. infobox 内 3 处表格改造：plain tabular → tabularx{\linewidth} 撑满 + `\captionof`+`\label`（tab:vc-history / tab:centralized-vs-distributed / tab:git-vs-github）
- [x] 3. 独立 table 4 处改造：tabularx{\textwidth} 撑满 + `\caption`+`\label`（tab:config-levels / tab:file-states / tab:branch-naming / tab:recommended-extensions）
- [x] 4. longtable 7 处首行加 `\caption`+`\label`（tab:pitfalls / tab:common-errors / tab:cmd-basic / tab:cmd-branch / tab:cmd-remote / tab:cmd-undo / tab:vscode-shortcuts）
- [x] 5. 正文 8 处补 `\ref{tab:xxx}` 引用（插入点表见 Execution strategy）
- [x] 6. AGENTS.md 行 55 修订：infobox 内改用 tabularx 撑满内宽，保留禁 tcolorbox 的 tabularx 键
- [x] 7. 两次 xelatex 编译通过 + PDF 14 处表格视觉抽查

## Final verification wave
- [x] F1. 计划合规审计：14 处表格全部改造（3+4+7）、8 处 ref 齐全、Scope OUT 零侵入（章节/环境定义/sc-*.md/images/37 图体系均未动）——VERDICT APPROVE
- [x] F2. 代码质量审查：LaTeX 语法正确、宏包使用合规（无新包）、样式与 AGENTS.md 修订后规范一致、无残留 `\arrayrulecolor{black}` 无意义调用——VERDICT APPROVE
- [x] F3. 真实手动 QA：打开编译产物 PDF，逐一核对 14 处表格（居中、撑满、表号「表 N.M」、正文 ref 解析正确、跨页行为正常）——VERDICT APPROVE
- [x] F4. 范围保真：对照 Scope IN/OUT 核对实际 diff，无超范围改动（git diff 或文件比对确认）——VERDICT APPROVE

## Commit strategy
- 本项目 AGENTS.md 安全边界：**Never 自动提交 git**。
- 执行者完成全部 todo + Final wave 后，不执行任何 git add/commit/push；交付物为修改后的 `Git_VSCode_Tutorial.tex`、`AGENTS.md` 及编译证据（`.log`、`.pdf`）。是否提交由用户自行决定。

## Success criteria
1. 两次 xelatex 编译均 exit 0、无致命错误
2. 14 处表格全部具备 `\caption`/`\captionof` + `\label{tab:xxx}`，表号渲染为「表 N.M」
3. infobox 内 3 处表格撑满 infobox 内宽（左右无大片空白）
4. 独立 table 4 处撑满 textwidth
5. 正文 8 处 `\ref{tab:xxx}` 全部解析为正确表号（第二次编译后无 undefined reference）
6. AGENTS.md 行 55 已修订并与实码一致
7. 37 图引用体系、章节结构、tcolorbox 环境定义、sc-*.md、images/ 均未受影响
