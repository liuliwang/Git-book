# table-styling-fix - Draft

## State
- intent: clear
- review_required: false
- classification: Standard
- slug: table-styling-fix
- status: plan-written (awaiting delivery; Metis gap analysis DONE, 11 findings folded)
- plan_path: .omo/plans/table-styling-fix.md
- metis_session: ses_03a64add0ffei52b6VySicN50l
- metis_findings: 1 blocker (误报, 计划未写错列规格但已采纳"写明实际列规格"建议) + 5 major + 5 minor, 已全部折叠进计划 (X 列 raggedright、infobox 选项保留、longtable 示例、Todo5 依赖、自动化 QA、Scope OUT 补充)

## Request
1.1 节「版本控制的发展简史」infobox 内表格排版难看（未居中、列宽窄、右侧大片空白），其他章节存在类似问题。修复全文档表格排版并统一编号体系。

## Forks (user-answered)
- **Fork1 infobox 表格宽度策略 → 选项2「允许 tabularx 撑满内宽」**（需修订 AGENTS.md 行55 规范）
- **Fork2 表格编号体系范围 → 选项2「全部表格加编号」**（独立 table + longtable + infobox 内表格共 14 处全加 caption+label+ref）

## Components ledger
| id | outcome | status | evidence |
|----|---------|--------|----------|
| A | infobox 内表格改用 tabularx 撑满内宽 + captionof 编号 | planned | tex 行 426/453/675; AGENTS.md 行55 |
| B | 独立 table 浮动表格改用 tabularx 撑满 textwidth + caption+label | planned | tex 行 938/1185/1715/2439 |
| C | 全部表格加 caption+label 编号体系 + 正文 ref 引用 | planned | tex 14 处表格 |
| D | longtable 速查表加 caption+label + 正文 ref | planned | tex 行 2721/2767/3012/3038/3059/3080/3101 |
| E | 两次 xelatex 编译验证通过 | planned | AGENTS.md 构建命令 |
| F | AGENTS.md 行55 规范修订（允许 infobox 内 tabularx 宏包环境） | planned | AGENTS.md 行55 |
| G | 导言区加 \renewcommand{\tablename}{表}（表号中文化） | planned | tex 行135 仅 figurename 中文化 |

## Approach
1. 导言区行135 附近补 `\renewcommand{\tablename}{表}`（与 figurename=图 一致，表号渲染为「表 N.M」）
2. infobox 内 3 处表格（行426/453/675）：plain tabular + 局部组 → `\begin{tabularx}{\linewidth}{...}` 撑满 infobox 内宽；原 `\textbf{标题}` → `\captionof{table}{标题}\label{tab:xxx}`
3. 独立 table 4 处（行938/1185/1715/2439）：plain tabular → tabularx{\textwidth} 撑满（行2439 已是 tabularx，仅加 caption+label）；加 `\caption{...}\label{tab:xxx}`
4. longtable 7 处（行2721/2767/3012/3038/3059/3080/3101）：`\begin{longtable}{...}` 后首行加 `\caption{...}\label{tab:xxx}\\`
5. 正文 14 处补 `\ref{tab:xxx}` 引用（与 37 图体系一致）
6. AGENTS.md 行55 修订：放宽为「infobox 内用 tabularx 宏包环境 `\begin{tabularx}{\linewidth}{...}` 撑满内宽」；保留「禁用 tcolorbox 的 tabularx 键」（后者会劫持 booktabs 规则色，TeX.SE 380361）
7. 两次 xelatex 编译验证通过（无致命错误，允许 Overfull/Underfull 警告）

## Scope IN
- Git_VSCode_Tutorial.tex 导言区（行135 附近加 tablename）
- 14 处表格（3 infobox + 4 table + 7 longtable）的 tabular/tabularx/longtable 环境 + caption+label
- 正文 14 处补 \ref{tab:xxx) 引用
- AGENTS.md 行55 表格样式约定修订

## Scope OUT (Must-NOT-Have)
- 不新增/删除章节结构
- 不修改 tcolorbox 环境定义（tipbox/warnbox/infobox/keybox/stepbox 的图标/标签/颜色）
- 不修改 sc-*.md 参考文档
- 不修改 images/ 目录
- 不引入新宏包（caption/tabularx/longtable 均已加载）
- 不改动既有 37 图引用体系
- 不自动提交 git
- 不放宽「不得新增环境类型」约束

## Next workflow action
Awaiting user approval to write .omo/plans/table-styling-fix.md. Approval authorizes plan creation only; execution starts in separate worker session (e.g. $start-work). After approval: scaffold plan file, run mandatory Metis gap analysis, APPEND todo batches, fill TL;DR last, self-review, then present handoff.
