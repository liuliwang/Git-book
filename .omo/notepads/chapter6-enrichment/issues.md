# Issues — chapter6-enrichment

Problems and gotchas encountered during work on this plan.

_Auto-scaffolded by /start-work. Append new entries below - never overwrite._

---

## 2026-08-04 交叉引用检查发现并已修复
- 修复 11 处新增 label 无人引用：10 个 figure label 补充正文"如图~\ref{...} 所示"，1 个 sec:git-worktrees 补充前向引用。
- LSP 对 tex 的 Undefined reference 为误报（宏参数内 label 无法解析），以 xelatex .log 为准。
- 既有 Overfull \hbox：593-595、2400-2401、2472、3204-3205、3300-3302、3458 行，不在本次改动范围，留待后续章节处理。