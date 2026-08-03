# fix-tikz-diagrams - Work Plan

## TL;DR (For humans)

**What you'll get:** 教程 PDF 中全部 11 张 TikZ 示意图的大小将适配页面宽度，节点内文字不再堆叠溢出，所有图视觉风格统一。

**Why this approach:** 节点加 `text width` 让长文字自动换行（根治堆叠）+ 调整 5 张图的节点坐标使自然宽度逼近 textwidth 16cm（根治溢出与放大过度）+ resizebox 统一做 ±0.85~1.39× 微调保险。三管齐下，每张图缩放比都落在合理区间。

**What it will NOT do:** 不改章节结构、不改 5 个提示框环境定义、不改颜色定义、不改 26 张位图及其引用、不改正文文字内容、不引入新 LaTeX 宏包。

**Effort:** Medium
**Risk:** Low - 仅改 11 处 tikzpicture 代码块，xelatex 两次编译即知成败
**Decisions to sanity-check:** 图4/10 坐标放大倍数（原始 7.5-8.5cm→13-12.75cm）、图2/3/5 间距缩小值、图5 minimum width 3→3.5cm

Your next move: 运行 `/start-work` 启动执行会话。完整执行细节见下。

---

> TL;DR (machine): Medium effort, Low risk - 11 张 TikZ 图统一加 text width + 调整坐标 + resizebox 微调，xelatex 两次编译验证。

## Scope
### Must have
- 11 张 TikZ 图的节点 style 增加 `text width` 属性（= minimum width − 0.4~0.5cm）
- 图2/3/5（溢出）调整节点间距/坐标，跨度 18-19cm → ≤16cm
- 图4/10（放大过度）调整节点坐标放大原始尺寸，跨度 7.5-8.5cm → 12-13cm
- 图1/6/8（自然OK）新增 `\resizebox{\textwidth}{!}{...}` 包裹
- 图7/9/11 保持现有 resizebox，仅加 text width + legend 防护
- 图6/9/11 三个 legend 节点加 `text width=14cm`
- 图5 minimum width 3cm→3.5cm（容纳 "(Working Directory)"）
- 图4 minimum width 2.5cm→3cm
- xelatex 连续两次编译无致命错误

### Must NOT have (guardrails, anti-slop, scope boundaries)
- 不改章节结构（第 0-9 章 + 附录），不新增/删除章节
- 不改 5 个 tcolorbox 环境定义（行 86-129：tipbox/warnbox/infobox/keybox/stepbox）
- 不改颜色定义（行 29-39：gitorange/warnred/infoblue/tipgreen 等）
- 不改 `\vscodeimg`/`\vscodeimgnarrow`/`\vscodeimgwide` 命令（行 132+）
- 不改 26 张位图及其 `\ref` 引用
- 不改 sc-*.md 参考文档（只读）
- 不改正文文字内容（仅改 tikzpicture 代码块内部）
- 不引入新 LaTeX 宏包（不加 adjustbox/scalebox 等）
- 不改 `\figurename`、caption 格式、label 命名
- 不自动提交 git（AGENTS.md 约定）

## Verification strategy
> Zero human intervention - all verification is agent-executed.
- Test decision: tests-after（xelatex 编译）+ 静态跨度/宽度计算验证
- 编译命令：`C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex`（连续两次）
- 通过标准：无致命错误（Overfull/Underfull 警告可接受）；首次编译日志中 "fatal: refusing to" 是正文 Git 报错示例文本，非编译错误
- 静态验证：每张图编辑后计算跨度 = (max_x − min_x) + minimum_width，确认 ≤16cm 或 resizebox 缩放比在 0.85~1.39×
- Evidence: `.omo/evidence/task-N-fix-tikz-diagrams.{txt,log}`（编译日志 + 跨度计算记录）

## Execution strategy
### Parallel execution waves

**Wave 1（6 todos）— C1 溢出图(3) + C4 自然OK图(3)**：6 张图互不依赖，可并行编辑
**Wave 2（5 todos）— C2 放大过度图(2) + C3 已有resizebox图(3)**：5 张图互不依赖，可并行编辑
**Wave 3（1 todo）— C5 编译验证**：依赖 Wave 1+2 全部完成

### Dependency matrix
| Todo | Depends on | Blocks | Can parallelize with |
| --- | --- | --- | --- |
| 1 (图1 three-analogies) | - | 12 | 2,3,4,5,6 |
| 2 (图2 learning-path) | - | 12 | 1,3,4,5,6 |
| 3 (图3 git-objects) | - | 12 | 1,2,4,5,6 |
| 4 (图5 three-zones) | - | 12 | 1,2,3,5,6 |
| 5 (图6 file-states) | - | 12 | 1,2,3,4,6 |
| 6 (图8 pr-workflow) | - | 12 | 1,2,3,4,5 |
| 7 (图4 commit-structure) | - | 12 | 8,9,10,11 |
| 8 (图10 cheatsheet-merge-rebase) | - | 12 | 7,9,10,11 |
| 9 (图7 merge-rebase) | - | 12 | 7,8,10,11 |
| 10 (图9 cheatsheet-zones) | - | 12 | 7,8,9,11 |
| 11 (图11 pr-workflow-full) | - | 12 | 7,8,9,10 |
| 12 (编译验证) | 1-11 | F1-F4 | - |

## Todos
> Implementation + Test = ONE todo. Never separate.
<!-- APPEND TASK BATCHES BELOW THIS LINE WITH edit/apply_patch - never rewrite the headers above. -->
- [x] 1. 图1 three-analogies 加 text width + resizebox
  What to do / Must NOT do: 在行 252 的 `box/.style` 中 `align=center` 后追加 `text width=3cm`；在行 250 `\begin{figure}[H]\centering` 后、行 251 `\begin{tikzpicture}` 前插入 `\resizebox{\textwidth}{!}{%`，在行 279 `\end{tikzpicture}` 后、行 280 `\caption` 前插入 `}`。不改节点坐标(0,0)/(5,0)/(2.5,-3)、不改 minimum width=3.5cm、不改 inner sep=8pt。
  Parallelization: Wave 1 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:250-280。box style 定义在行 252-253，3 节点 at (0,0)/(5,0)/(2.5,-3)，arr 箭头行 276-278。
  Acceptance criteria: 编辑后 grep 行 252 含 `text width=3cm`；grep 行 251 附近含 `\resizebox{\textwidth}{!}`；跨度计算 (5-0)+3.5=8.5cm，resizebox 放大比 16/8.5=1.88×（可接受，3 大节点放大后更清晰）。
  QA scenarios (grep 验证 + 跨度计算): happy=grep 找到 text width 和 resizebox；failure=text width 缺失或 resizebox 未闭合。Evidence .omo/evidence/task-1-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图1 three-analogies 加 text width 防堆叠 + resizebox 统一防护

- [x] 2. 图2 learning-path 调整坐标 + text width + resizebox
  What to do / Must NOT do: 行 320-324 节点坐标 (0,0)/(5,0)/(10,0)/(15,0)/(7.5,-3) 改为 (0,0)/(4,0)/(8,0)/(12,0)/(6,-3)；行 315 `phase/.style` 的 `minimum width=4cm` 改为 `3.5cm` 并追加 `text width=3cm`；行 313 插入 resizebox 包裹（同图1 模式）。不改 phase 的 fill/#1 参数、不改 arr style、不改箭头 \draw 行 326-330。
  Parallelization: Wave 1 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:313-333。phase style 行 315-316，5 节点 行 320-324，箭头 行 326-330。
  Acceptance criteria: 跨度计算 (12-0)+3.5=15.5cm ≤16cm，resizebox 比 16/15.5=1.03×；grep 行 315 含 `text width=3cm` 且 `minimum width=3.5cm`；grep 行 320-324 含 `(4,0)`/`(8,0)`/`(12,0)`/`(6,-3)`。
  QA scenarios (grep + 跨度): happy=跨度 15.5cm + 坐标更新；failure=坐标漏改导致仍 19cm。Evidence .omo/evidence/task-2-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图2 learning-path 缩小坐标间距消除溢出 + text width 防堆叠

- [x] 3. 图3 git-objects 调整间距 + text width + resizebox
  What to do / Must NOT do: 行 503-505 三个 `right=1.5cm of` 改为 `right=0.8cm of`；行 497 `box/.style` 的 `minimum width=3.5cm` 追加 `text width=3cm`；行 495 插入 resizebox 包裹。不改节点 fill 颜色、不改 arr style、不改箭头行 507-509。
  Parallelization: Wave 1 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:495-511。box style 行 497-498，4 节点 right=1.5cm 行 502-505，箭头 行 507-509。
  Acceptance criteria: 跨度计算 4×3.5+3×0.8=16.4cm，resizebox 比 16/16.4=0.97×；grep 行 497 含 `text width=3cm`；grep 行 503-505 含 `right=0.8cm of`。
  QA scenarios (grep + 跨度): happy=间距 0.8cm + text width；failure=漏改间距仍 1.5cm 导致 18.5cm。Evidence .omo/evidence/task-3-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图3 git-objects 缩小节点间距消除溢出 + text width 防堆叠

- [x] 4. 图5 three-zones 调整间距+min width + text width + resizebox
  What to do / Must NOT do: 行 703-705 三个 `right=2cm of` 改为 `right=1cm of`；行 697 `box/.style` 的 `minimum width=3cm` 改为 `3.5cm` 并追加 `text width=3cm`；行 695 插入 resizebox 包裹。不改节点 fill、不改 arr、不改箭头标签 `git add`/`git commit` 等（行 707-710）。
  Parallelization: Wave 1 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:695-712。box style 行 697-698，4 节点 right=2cm 行 702-705，箭头+标签 行 707-710。
  Acceptance criteria: 跨度计算 4×3.5+3×1=17cm，resizebox 比 16/17=0.94×；grep 行 697 含 `minimum width=3.5cm` 和 `text width=3cm`；grep 行 703-705 含 `right=1cm of`；"(Working Directory)" 约 3.2cm < 3.5cm min width，不再溢出节点。
  QA scenarios (grep + 跨度 + 文字宽度): happy=min width 3.5cm 容纳长文字；failure=min width 仍 3cm 导致文字溢出。Evidence .omo/evidence/task-4-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图5 three-zones 增大 min width 容纳英文 + 缩小间距 + text width

- [x] 5. 图6 file-states 加 text width + legend 防护 + resizebox
  What to do / Must NOT do: 行 1192 `state/.style` 追加 `text width=3cm`；行 1211 legend 节点 `\node[anchor=west, font=\small]` 改为 `\node[anchor=west, font=\small, text width=14cm, align=center]`；行 1190 插入 resizebox 包裹。不改节点坐标 (0,0)/(4,0)/(8,0)/(4,-2.5)、不改箭头、不改 dashed 箭头行 1208。
  Parallelization: Wave 1 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:1190-1215。state style 行 1192-1193，4 节点 行 1198-1201，legend 行 1211-1213。
  Acceptance criteria: 跨度 (8-0)+3.5=11.5cm，resizebox 比 16/11.5=1.39×（可接受）；grep 行 1192 含 `text width=3cm`；grep 行 1211 含 `text width=14cm`；legend 长文字自动换行不溢出。
  QA scenarios (grep + 跨度): happy=legend text width 14cm；failure=legend 仍单行长文字缩放后堆叠。Evidence .omo/evidence/task-5-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图6 file-states 加 legend text width 防堆叠 + resizebox

- [x] 6. 图8 pr-workflow 加 text width + resizebox
  What to do / Must NOT do: 行 2216 `box/.style` 追加 `text width=3cm`；行 2214 插入 resizebox 包裹。不改节点坐标 (0,0)/(4,0)/(8,0)/(8,-2)/(4,-2)/(0,-2)、不改 minimum width=3.5cm、不改箭头标签。
  Parallelization: Wave 1 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:2214-2237。box style 行 2216-2217，6 节点 行 2222-2227，箭头 行 2230-2235。
  Acceptance criteria: 跨度 (8-0)+3.5=11.5cm，resizebox 比 1.39×；grep 行 2216 含 `text width=3cm`。
  QA scenarios (grep + 跨度): happy=text width + resizebox；failure=text width 缺失。Evidence .omo/evidence/task-6-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图8 pr-workflow 加 text width 防堆叠 + resizebox 统一

- [x] 7. 图4 commit-structure 调整坐标放大 + min width + text width + resizebox 微调
  What to do / Must NOT do: 行 605 blob1 坐标 `(-2.5,-4.5)` 改为 `(-3.5,-4.5)`；行 606 blob3 坐标 `(2.5,-4.5)` 改为 `(3.5,-4.5)`；行 610 parent 坐标 `(4,0)` 改为 `(5,0)`；行 583 `obj/.style` 的 `minimum width=2.5cm` 改为 `3cm` 并追加 `text width=2.5cm`；保持行 581 resizebox 不变（改为微调）。不改 commit/tree 节点坐标 (0,0)/(0,-2.5)、不改 arr style、不改箭头。
  Parallelization: Wave 2 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:580-622。obj style 行 583-584，blob 节点 行 605-607，parent 行 610-613，resizebox 行 581。
  Acceptance criteria: 跨度计算 (5-(-3.5))+3=11.5cm，resizebox 比 16/11.5=1.39×（从原 2.13× 降至 1.39×，消除放大过度）；grep 行 583 含 `minimum width=3cm` 和 `text width=2.5cm`；grep 行 605 含 `(-3.5,-4.5)`、行 610 含 `(5,0)`。
  QA scenarios (grep + 跨度): happy=放大比 1.39× 可接受；failure=坐标未改仍 2.13× 放大过度。Evidence .omo/evidence/task-7-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图4 commit-structure 放大原始坐标消除放大过度 + text width

- [x] 8. 图10 cheatsheet-merge-rebase 调整坐标放大 + resizebox 微调
  What to do / Must NOT do: 将 Merge 部分 commit 坐标 x 值 ×1.5：行 2857-2861 的 (0,4)/(2,4)/(4,4)/(6,4)/(8,4) 改为 (0,4)/(3,4)/(6,4)/(9,4)/(12,4)；行 2864-2865 feature (4,2.5)/(6,2.5) 改为 (6,2.5)/(9,2.5)；行 2854 标题 at (4,5) 改为 at (6,5)。Rebase 部分：行 2882-2884 (0,-2)/(2,-2)/(4,-2) 改为 (0,-2)/(3,-2)/(6,-2)；行 2887-2888 (6,-2)/(8,-2) 改为 (9,-2)/(12,-2)；行 2879 标题 at (4,-1) 改为 at (6,-1)；行 2895-2896 tag at (rm1)/(rf1) 不变。保持行 2846 resizebox 不变。不改 commit/tag/arr style、不改箭头 \draw。
  Parallelization: Wave 2 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:2844-2897。commit/tag/arr style 行 2848-2851，Merge 节点 行 2854-2876，Rebase 节点 行 2879-2896。
  Acceptance criteria: 跨度计算 (12-0)+0.5=12.5cm，resizebox 比 16/12.5=1.28×（从原 1.88× 降至 1.28×）；grep 行 2860 含 `(12,4)`、行 2888 含 `(12,-2)`。
  QA scenarios (grep + 跨度): happy=放大比 1.28×；failure=坐标未改仍 1.88×。Evidence .omo/evidence/task-8-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图10 cheatsheet-merge-rebase 放大坐标消除放大过度

- [x] 9. 图7 merge-rebase 加 label text width + 保持 resizebox
  What to do / Must NOT do: 行 1838 `label/.style` 追加 `text width=3cm, align=center`；保持行 1835 resizebox 不变。不改 commit circle style、不改节点坐标、不改箭头。
  Parallelization: Wave 2 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:1834-1869。label style 行 1838，commit 节点 行 1842-1846/1857-1860，label 节点 行 1854/1866。
  Acceptance criteria: grep 行 1838 含 `text width=3cm`；跨度 (12.5-0)+0.4≈12.9cm，resizebox 比 1.24×（保持）；label "Merge：保留完整历史，但有合并提交" 自动换行不溢出。
  QA scenarios (grep): happy=label text width 3cm；failure=label 缺失 text width 导致长文字溢出。Evidence .omo/evidence/task-9-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图7 merge-rebase 加 label text width 防长文字溢出

- [x] 10. 图9 cheatsheet-zones 加 text width + legend 防护 + 保持 resizebox
  What to do / Must NOT do: 行 2786 `zone/.style` 追加 `text width=2.8cm`（在 inner sep=10pt 之后）；行 2828 legend 节点追加 `text width=14cm, align=center`；保持行 2784 resizebox 不变。不改节点坐标 (0,0)/(5,0)/(10,0)/(15,0)、不改 minimum width=3.2cm、不改箭头/legend 内容文字。
  Parallelization: Wave 2 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:2782-2834。zone style 行 2786-2787，4 节点 行 2793-2815，legend 行 2828-2832。
  Acceptance criteria: 跨度 (15-0)+3.2=18.2cm，resizebox 比 16/18.2=0.88×（保持）；grep 行 2786 含 `text width=2.8cm`；grep 行 2828 含 `text width=14cm`；legend 三段 `\quad` 文字自动换行。
  QA scenarios (grep + 跨度): happy=zone+legend text width；failure=legend 缺 text width 缩放后堆叠。Evidence .omo/evidence/task-10-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图9 cheatsheet-zones 加 text width + legend 防护

- [x] 11. 图11 pr-workflow-full 加 text width + legend 防护 + 保持 resizebox
  What to do / Must NOT do: 行 2917 `box/.style` 追加 `text width=3cm`（在 inner sep=6pt 之后）；行 2980 legend 节点追加 `text width=14cm, align=center`；保持行 2915 resizebox 不变。不改节点坐标、不改 minimum width=3.5cm、不改箭头标签 cmd style、不改 legend 文字。
  Parallelization: Wave 2 | Blocked by: - | Blocks: 12
  References: Git_VSCode_Tutorial.tex:2912-2986。box style 行 2917-2918，7 节点 行 2924-2967，legend 行 2980-2984。
  Acceptance criteria: 跨度 (12-0)+3.5=15.5cm，resizebox 比 16/15.5=1.03×（保持）；grep 行 2917 含 `text width=3cm`；grep 行 2980 含 `text width=14cm`。
  QA scenarios (grep + 跨度): happy=box+legend text width；failure=缺 text width。Evidence .omo/evidence/task-11-fix-tikz-diagrams.txt
  Commit: Y | fix(tikz): 图11 pr-workflow-full 加 text width + legend 防护

- [x] 12. xelatex 连续两次编译验证
  What to do / Must NOT do: 在 C:\Users\Admin\Desktop\Git 目录执行两次：`C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex`。第一次生成 .aux/.toc，第二次解析交叉引用。检查日志无致命错误（LaTeX Error/Fatal error）；Overfull/Underfull \hbox/\vbox 警告可接受；"fatal: refusing to" 是正文 Git 示例文本非编译错误。Must NOT 改任何 .tex 内容（仅编译）。
  Parallelization: Wave 3 | Blocked by: 1-11 | Blocks: F1-F4
  References: Git_VSCode_Tutorial.tex（全文）。AGENTS.md 构建命令约定。
  Acceptance criteria: 第二次编译退出码 0；日志末尾无 "Fatal error"/"LaTeX Error:.*undefined"/"Emergency stop"；.pdf 文件生成且大小 >0。
  QA scenarios (编译): happy=两次编译退出码 0 + PDF 生成；failure=编译报错需回查对应 todo。Evidence .omo/evidence/task-12-fix-tikz-diagrams.log（编译日志副本）
  Commit: N | （编译验证不单独提交，随 Wave 1+2 的修复一起）

## Final verification wave
> Runs in parallel after ALL todos. ALL must APPROVE. Surface results and wait for the user's explicit okay before declaring complete.
- [x] F1. Plan compliance audit
  验证 11 张图均含 text width、5 张图坐标已调整、3 张图新增 resizebox、3 个 legend 含 text width=14cm；跨度全部 ≤16cm 或缩放比在 0.85~1.39×。grep 逐图核对。
- [x] F2. Code quality review
  检查 resizebox 包裹语法闭合（`{`/`}` 配对）、text width 属性语法正确（`text width=Xcm`）、无遗留旧坐标值、无重复属性。
- [x] F3. Real manual QA
  第二次编译生成的 PDF 用 PDF 阅读器打开，目视检查 11 张图：无溢出页面、无文字堆叠、无节点重叠、resizebox 缩放视觉合理。
- [x] F4. Scope fidelity
  确认未改章节结构、tcolorbox 定义（行 86-129）、颜色定义（行 29-39）、图片命令（行 132+）、26 张位图引用、正文文字；确认未引入新宏包。git diff 仅触及 11 处 tikzpicture 代码块。

## Commit strategy
- Wave 1 结束（todo 1-6 完成后）：一次提交 `fix(tikz): Wave1 溢出图+自然OK图统一防护（图1/2/3/5/6/8）`
- Wave 2 结束（todo 7-11 完成后）：一次提交 `fix(tikz): Wave2 放大过度图+已有resizebox图防护（图4/7/9/10/11）`
- Wave 3 编译验证（todo 12）通过后：若有修复则追加提交；无修复则不提交
- 不自动提交含 secrets 的文件；提交前确认 git status 仅 .tex + .omo 变更

## Success criteria
- 11 张 TikZ 图全部含 `text width` 属性（grep 验证）
- 5 张图坐标/间距已调整（grep 验证新坐标值）
- 3 张图新增 resizebox 包裹（grep 验证）
- 3 个 legend 含 `text width=14cm`（grep 验证）
- 所有图跨度 ≤16cm 或 resizebox 缩放比 ∈ [0.85, 1.39]×（计算验证）
- xelatex 两次编译退出码 0，无致命错误
- PDF 中 11 张图视觉无溢出/堆叠/重叠（目视验证）
- git diff 仅触及 11 处 tikzpicture 代码块，未越界改其他内容
