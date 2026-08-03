---
slug: fix-tikz-diagrams
status: awaiting-approval
intent: clear
review_required: false
pending-action: write .omo/plans/fix-tikz-diagrams.md
approach: 全部 11 个 TikZ 图统一防护——节点加 text width 防文字堆叠 + 调整原始坐标使自然尺寸接近 textwidth(16cm) + resizebox 微调防溢出/放大过度
---

# Draft: fix-tikz-diagrams

## 页面基准
- A4 + margin=2.5cm → textwidth = 16cm
- 源文件唯一可编辑：Git_VSCode_Tutorial.tex（~3134 行）
- 编译：xelatex 连续两次（AGENTS.md 约定）

## Components (topology ledger)
| id | outcome | status | evidence |
|----|---------|--------|----------|
| C1 溢出图组 | 图2/3/5 调整坐标+text width+resizebox，跨度收敛到 ≤16cm | active | 行313/495/695 |
| C2 放大过度图组 | 图4/10 调整坐标放大原始尺寸，resizebox 改为微调 | active | 行580/2844 |
| C3 已有resizebox图组 | 图7/9/11 加 text width + legend 防护，保持 resizebox | active | 行1834/2782/2912 |
| C4 自然OK图组 | 图1/6/8 加 text width + resizebox 适度放大 | active | 行250/1190/2214 |
| C5 编译验证 | xelatex 两次编译通过，Overfull/Underfull 警告可接受 | active | 构建命令 |

## Open assumptions (announced defaults)
| assumption | adopted default | rationale | reversible? |
|------------|----------------|------------|-------------|
| 调整原始坐标是否可接受 | 是，调整节点间距/坐标使自然宽度接近 textwidth | 单纯 resizebox 对小图放大过度（图4 放大2.13×），调整坐标是最佳实践；可逆，仅改坐标数值 | 是 |
| text width 取值 | = minimum width - 0.4~0.5cm（扣 inner sep×2 + padding） | 让文字自动换行不溢出节点边界 | 是 |
| legend 长行处理 | 加 text width=14cm 让 \quad 分隔的长文字自动换行 | resizebox 缩放后单行长文字会挤压堆叠 | 是 |
| resizebox 统一策略 | 每图保留 resizebox{\textwidth}{!}，但通过坐标调整使缩放比在 0.85~1.25× 之间 | 避免放大过度（>1.5×）和缩小过度（<0.8×） | 是 |
| minimum width 调整 | 仅图5 增大（3→3.5cm 容纳"(Working Directory)"） | 其余图保持现有 minimum width，靠 text width 防溢出 | 是 |

## Findings (cited - path:lines)
- textwidth=16cm：行8 `\usepackage[margin=2.5cm,headheight=15pt]{geometry}` + A4(行5)
- 图1 three-analogies（行250-280）：跨度 8.5cm，3 节点 minimum width=3.5cm inner sep=8pt，无 resizebox — 自然 OK
- 图2 learning-path（行313-333）：5 节点 at x=0,5,10,15 + (7.5,-3)，minimum width=4cm，跨度 19cm — **溢出 3cm**
- 图3 git-objects（行495-511）：4 节点 right=1.5cm，minimum width=3.5cm，跨度 18.5cm — **溢出 2.5cm**
- 图4 commit-structure（行580-622）：resizebox 包裹，原始跨度 7.5cm — **放大 2.13×，文字异常大**
- 图5 three-zones（行695-712）：4 节点 right=2cm，minimum width=3cm，跨度 18cm — **溢出 + 文字溢出节点**（"(Working Directory)" > 3cm）
- 图6 file-states（行1190-1215）：跨度 11.5cm，legend 行1212 单行长文字 — legend 堆叠隐患
- 图7 merge-rebase（行1834-1869）：resizebox 包裹，跨度 13cm，放大 1.2× — 基本 OK，label 长文字
- 图8 pr-workflow（行2214-2237）：跨度 11.5cm，无 resizebox — 自然 OK
- 图9 cheatsheet-zones（行2782-2834）：resizebox 包裹，跨度 18.2cm，缩小 0.88× — legend 行2828 长
- 图10 cheatsheet-merge-rebase（行2844-2897）：resizebox 包裹，原始跨度 8.5cm — **放大 1.88×**
- 图11 pr-workflow-full（行2912-2986）：resizebox 包裹，跨度 15.5cm，放大 1.03× — legend 行2980 长
- 节点 style 均用 `\\` 硬换行，无 `text width` 属性 — **文字堆叠根因**：行宽 > minimum width 时溢出

## Decisions (with rationale)
1. **范围=全部 11 图**（用户选）：统一防护，风格一致。
2. **堆叠修复=text width 自动换行**（用户选）：节点 style 加 `text width`，让长文字自动换行而非 `\\` 硬换行。
3. **resizebox 统一保留**（默认）：每图加/保留 resizebox{\textwidth}{!} 做保险，但通过坐标调整把缩放比控制在 0.85~1.25×。
4. **放大过度图调整坐标**（默认最佳实践）：图4/10 原始跨度太小，放大节点间距使自然宽度接近 14-15cm，resizebox 仅微调。
5. **溢出图调整坐标**（默认最佳实践）：图2/3/5 缩小节点间距使跨度 ≤16cm，resizebox 兜底。
6. **text width 取值规则**（默认）：text width = minimum width - 0.4~0.5cm。
7. **legend 加 text width=14cm**（默认）：防 `\quad` 长行缩放后堆叠。

## 逐图修复方案
| 图 | 行 | 修复动作 | 目标跨度 | resizebox 比 |
|----|----|---------|---------|------------|
| 图1 three-analogies | 250-280 | 节点加 text width=3cm + 加 resizebox | 8.5cm | 放大1.88×（可接受，3大节点） |
| 图2 learning-path | 313-333 | x 坐标 0/5/10/15→0/4/8/12 + (7.5→6,-3) + text width=3cm + resizebox | 16cm | 1.0× |
| 图3 git-objects | 495-511 | right 1.5→0.8cm + text width=3cm + resizebox | 16cm | 1.0× |
| 图4 commit-structure | 580-622 | blob x -2.5→-3.5/2.5→3.5 + parent 4→5 + min width 2.5→3 + text width=2.5cm + resizebox | 13cm | 放大1.23× |
| 图5 three-zones | 695-712 | right 2→1cm + min width 3→3.5cm + text width=3cm + resizebox | 17cm | 缩小0.94× |
| 图6 file-states | 1190-1215 | text width=3cm + legend text width=14cm + resizebox | 11.5cm | 放大1.39× |
| 图7 merge-rebase | 1834-1869 | label text width=3cm + 保持 resizebox | 13cm | 放大1.2×（保持） |
| 图8 pr-workflow | 2214-2237 | text width=3cm + resizebox | 11.5cm | 放大1.39× |
| 图9 cheatsheet-zones | 2782-2834 | text width=2.8cm + legend text width=14cm + 保持 resizebox | 18.2cm | 缩小0.88×（保持） |
| 图10 cheatsheet-merge-rebase | 2844-2897 | commit 间距 2→3cm（x 坐标×1.5） + 保持 resizebox | 12.75cm | 放大1.25× |
| 图11 pr-workflow-full | 2912-2986 | text width=3cm + legend text width=14cm + 保持 resizebox | 15.5cm | 放大1.03×（保持） |

## Scope IN
- 11 个 TikZ 图的 tikzpicture 节点 style：加 text width 属性
- 5 个图（图2/3/4/5/10）的节点坐标/间距调整
- 3 个图（图1/6/8）新增 resizebox 包裹
- 3 个 legend 节点（图6/9/11）加 text width
- 图5 minimum width 3→3.5cm
- 图4 minimum width 2.5→3cm

## Scope OUT (Must NOT have)
- 不改章节结构、不新增/删除章节
- 不改 tcolorbox 5 个环境的定义（行86-129）
- 不改颜色定义（行29-39）
- 不改 \vscodeimg 等图片命令（行132+）
- 不改 26 张位图及其引用
- 不改 sc-*.md 参考文档（只读）
- 不改正文文字内容（仅改 TikZ 图代码）
- 不引入新 LaTeX 宏包

## Open questions
无（范围与堆叠策略已由用户回答）。

## Approval gate
status: approved
approach: 全部 11 图统一防护——节点加 text width 防文字堆叠 + 调整坐标使自然尺寸接近 textwidth(16cm) + resizebox 微调防溢出/放大过度。
plan: .omo/plans/fix-tikz-diagrams.md（12 todos + F1-F4 验证波，已写）
next: 计划已就绪，等用户启动 /start-work 执行会话
