# Learnings — chapter6-enrichment

Conventions, patterns, and successful approaches discovered during work on this plan.

_Auto-scaffolded by /start-work. Append new entries below - never overwrite._

---

## 2026-08-04 交叉引用全面检查
- vscodeimg 宏第 3 参数是 label（非字面 \label），grep 必须用模式 \vscodeimg(narrow|wide)?\{...\}\{...\}\{([^}]+)\} 提取 figure label，不能只 grep \label{}。
- 新增 figure label 若正文无"如图~\ref{fig:xxx} 所示"即无人引用；第6章 10 个新图全部补上了正文引用（diff-editor-sidebyside/inline、gutter-indicators/hover、ai-commit-message/code-review、worktree-create/repositories、merge-editor-3way/inline-actions）。
- sec:git-worktrees 无引用，在 2810 行（分支管理详解）补了"第~\ref{sec:git-worktrees}~节"前向引用。
- 来源 URL 校验：VS Code 官方 sourcecontrol 五页 + GitKraken 两链接全部 200 可访问。
- xelatex 双次编译后 .log 无 undefined reference、无 Fatal；Overfull 均为既有问题（改动行范围外），可忽略。
## 2026-08-04 图片完整性验证 F2（独立验证者）
- 38 处 \vscodeimg* 引用（含 3 个变体宏）全部对应真实文件，无一缺失。
- images/ 现有 42 张编号图 + merge-conflict.png 共 43 个文件。
- 10 张新图（33-42）全部就位且大小远超 5KB 阈值：33→87KB, 34→61KB, 35→321KB, 36→125KB, 37→99KB, 38→99KB, 39→78KB, 40→31KB, 41→100KB, 42→57KB。
- VERDICT: APPROVE（无缺失、无非空问题）。
## 2026-08-04 编译验证 F1（独立验证者）
- xelatex 双次编译均成功，PDF 111 页（> 100 阈值）。
- .log 无 Fatal error / Emergency stop；无 undefined reference；LaTeX Warning 计数为 0。
- Overfull 共 6 处：593-595、2400-2401、2472、3204-3205、3300-3302、3458，与既有清单逐一完全匹配，无新增（3204-3205 虽在 2540-3210 检查范围内，但属已知既有问题）。
- VERDICT: APPROVE。

## 2026-08-04 交叉引用验证（波F3）— VERDICT: APPROVE
- xelatex 编译成功（111 页），.log 无 undefined reference、无 fatal error（权威依据）。
- ref 集合（59 个唯一）全部命中 label，0 个孤儿 ref（ref ⊆ label 成立）。
- 第6章 14 个新 label（sec:merge-conflicts/diff-editor/ai-git/git-worktrees + 10 个 fig:）全部被正文 \ref 引用。
- 备注（非阻塞）：11 个 label 定义后未被 \ref —— fig:ext-gh-pr、fig:ext-git-graph、fig:ext-git-history、fig:ext-gitlens-blame、fig:ext-gitlens-graph（第7章扩展图）、tab:cmd-basic、tab:cmd-branch、tab:cmd-remote、tab:cmd-undo、tab:common-errors、tab:vscode-shortcuts（第9章附录速查表）。均为第6章范围外，不影响本次验收。

## 2026-08-04 来源标注验证（波F4，独立验证者）— VERDICT: APPROVE
- 第6章范围 = 行 2438-3216；范围内 7 处 \url{} 全部命中官方文档。
- 新增 4 章节 infobox 全部就位：Diff 编辑器详解→staging-commits(2671)、AI 辅助功能→overview+staging-commits(2793/2795)、Git Worktrees→branches-worktrees(2899)、合并冲突解决→merge-conflicts(3005)。
- 优化章节：GitLens 进阶→gitlens-start-here+gitlens-16(3101/3104)、GitHub PR 进阶→sourcecontrol/github(3137)。源代码管理视图/暂存与提交由 Diff 节末尾 infobox(2667-2672，"本章内容参考"措辞，staging-commits URL) 覆盖，语义匹配。
- 【待核实】全书 grep = 0 匹配，第6章范围内 = 0。
- URL 抽查 3 个（staging-commits、merge-conflicts、gitlens-start-here）HEAD 均 200；与 T8 继承清单 7 个 URL 逐字一致，全部为 VS Code/GitKraken 官方域名。
- 备注（非阻塞）：源代码管理视图、暂存与提交两节无独立 infobox，依赖相邻 Diff 节 infobox 的"本章内容参考"表述覆盖，不构成缺失。