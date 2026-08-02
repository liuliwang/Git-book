# tikz-cross-refs - Work Plan

## TL;DR (For humans)

为 11 个 TikZ 示意图建立正式交叉引用：将 `\begin{center}...\end{center}` 包装转为 `\begin{figure}[H]\centering...\caption{...}\label{...}\end{figure}`，每图补 caption（名称——说明格式，与现有 26 处位图一致）+ label 键，正文 11 处引导句插入 `\ref`。5 个 resizebox 图的 caption/label 放在 resizebox 闭合 `}` 后。3 个实现 todo（T1=第0-1章5图、T2=第3-5章3图、T3=第9章3图+首次编译），T1-T2 纯编辑不编译，T3 末首次 xelatex 两次编译。F1-F4 最终验证。新增 11 图会重排现有 26 位图编号，但 `\ref` 两次编译后自动解析。TikZ 图内容（节点/箭头/样式）零改动。

## Scope

**IN**：唯一文件 `C:\Users\Admin\Desktop\Git\Git_VSCode_Tutorial.tex`
- 11 个 TikZ 示意图：`\begin{center}...\end{center}` → `\begin{figure}[H]\centering...\caption{...}\label{...}\end{figure}`
- 5 个 resizebox 图（#4 #7 #9 #10 #11）：caption/label 放在 resizebox 闭合 `}` 之后、`\end{figure}` 之前
- 11 处正文引导句插入 `\ref{fig:xxx}` 交叉引用
- 全部编辑完成后 xelatex 两次编译验证

**OUT**（Must-NOT-Have）：
- 不改 TikZ 图内容（节点、箭头、样式定义零改动）
- 不改现有 26 处 `\vscodeimg` 位图的 caption/label/ref
- 不引入新 LaTeX 包（`float`/`graphicx`/`tikz` 已加载）
- 不改 tcolorbox 环境定义
- 不改 `images/` 目录
- 不改 `sc-*.md` 参考文档
- 不改首行 `% !TEX program = xelatex`
- 不改章节结构
- T1-T2 期间不编译（grep 校验即可）

## Verification strategy

agent-executed QA，零人工介入：

1. **编辑期校验（T1-T2）**：grep 校验每处 `\begin{figure}[H]` 新增 + `\label{fig:` 新增 + 引导句已替换。不编译——每个图替换是独立的 `\begin{center}→\begin{figure}` 1:1 转换，不影响其他图，但全部改完再编译更高效。

2. **首次编译（T3 末）**：`C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex` 两次；无致命错误即通过（Overfull/Underfull 警告允许）。编译日志须无 `LaTeX Warning: Reference ... undefined`、无 `Label ... multiply defined`。

3. **引用一致性 grep 校验（T3/F1）**：
   - 新增 `\label{fig:` 计数 = 11（仅 TikZ 图，不含现有 26 处位图）
   - 新增 `\ref{fig:` 计数 = 11
   - 每个 `\ref{fig:xxx}` 必有同键 `\label{fig:xxx}`

4. **图编号变化说明**：新增 11 个 figure 会改变现有 26 个位图的编号（图序重排），但 `\ref` 自动解析，两次编译后所有引用编号正确。非错误。

5. **失败 QA 场景**：
   - `! Undefined control sequence` → `\caption` 或 `\label` 语法错误
   - `Reference ... undefined` → label 拼写与 \ref 不一致
   - `Label ... multiply defined` → label 键冲突（与现有 26 处位图 label 撞名）
   - `! Missing $ inserted` 或 `! Extra }` → figure 环境括号不匹配（resizebox 图尤其注意 `}` 闭合）

## Execution strategy

**编译策略**：T1-T2 编辑期不编译（grep 校验），T3 末首次 xelatex 两次编译。原因：每个图的 `\begin{center}→\begin{figure}` 是独立 1:1 转换，但逐个编译效率低且中间状态无意义。

**逐图替换表**（decision-complete，执行者零判断）：

| # | tikz 行 | label 键 | caption | \ref 引导句行 | 原文 → 目标措辞 | resizebox |
|---|---------|----------|---------|---------------|----------------|------------|
| 1 | 250 | fig:three-analogies | Git、GitHub 与 VS Code 的关系——三者协作构成完整的版本控制工作流 | 247 | \section{三个核心类比} 后插"三者关系如图~\ref{fig:three-analogies} 所示——" | 无 |
| 2 | 312 | fig:learning-path | 本教程学习路径——从心智模型到实战工作流的章节安排 | 309 | \section{本教程的学习路径图} 后插"完整路径如图~\ref{fig:learning-path} 所示——" | 无 |
| 3 | 494 | fig:git-objects | Git 的四个核心对象——Blob、Tree、Commit、Branch 的层次关系 | 491 | "以下四种对象：" → "以下四种对象，如图~\ref{fig:git-objects} 所示：" | 无 |
| 4 | 580 | fig:commit-structure | 一次提交的完整数据模型——Commit 指向 Tree，Tree 指向 Blob，并记录父提交 | 576 | "下图展示了一次提交的完整数据模型：" → "一次提交的完整数据模型如图~\ref{fig:commit-structure} 所示：" | 有 |
| 5 | 694 | fig:three-zones | Git 三大工作区域与远程仓库——工作区、暂存区、本地仓库、远程仓库之间的数据流转 | 691 | "请务必理解：" → "请务必理解，如图~\ref{fig:three-zones} 所示：" | 无 |
| 6 | 1189 | fig:file-states | 文件状态生命周期——未跟踪、已修改、已暂存、未修改之间的转换关系 | 1186 | "文件状态的生命周期如下：" → "文件状态的生命周期如图~\ref{fig:file-states} 所示：" | 无 |
| 7 | 1832 | fig:merge-rebase | Merge 与 Rebase 的对比——Merge 保留完整历史产生合并节点，Rebase 生成线性历史 | 1828 | \subsection{Rebase 与 Merge 的区别} 后插"两者对比如图~\ref{fig:merge-rebase} 所示——" | 有 |
| 8 | 2211 | fig:pr-workflow | Pull Request 完整工作流程——从创建分支到代码审查再到合并的协作循环 | 2208 | "下图展示了 Pull Request 的完整工作流程：" → "Pull Request 的完整工作流程如图~\ref{fig:pr-workflow} 所示：" | 无 |
| 9 | 2778 | fig:cheatsheet-zones | Git 四大区域数据流转速查——工作区、暂存区、本地仓库、远程仓库之间的完整操作路径 | 2774 | \section{速查卡 1：Git 四大区域数据流转} 后插"完整数据流转如图~\ref{fig:cheatsheet-zones} 所示——" | 有 |
| 10 | 2836 | fig:cheatsheet-merge-rebase | 分支演变对比速查——Merge 产生菱形合并节点，Rebase 生成线性历史 | 2832 | \section{速查卡 2：分支演变——合并 vs 变基} 后插"分支演变对比如图~\ref{fig:cheatsheet-merge-rebase} 所示——" | 有 |
| 11 | 2901 | fig:cheatsheet-remote | 远程协作完整链路速查——从本地开发到 GitHub 合并的完整流程 | 2897 | \section{速查卡 3：远程协作完整链路} 后插"完整协作链路如图~\ref{fig:cheatsheet-remote} 所示——" | 有 |

**Wrapper 转换模式**：

非 resizebox 图（#1,2,3,5,6,8）：
```latex
% 原（行 N）：
\begin{center}
% 原（行 N+1 起）：
\begin{tikzpicture}[...]
...
\end{tikzpicture}
% 原（行 M）：
\end{center}

% 改：
\begin{figure}[H]
\centering
\begin{tikzpicture}[...]
...
\end{tikzpicture}
\caption{<caption>}\label{<label>}
\end{figure}
```

resizebox 图（#4,7,9,10,11）：
```latex
% 原：
\begin{center}
\resizebox{\textwidth}{!}{%
\begin{tikzpicture}[...]
...
\end{tikzpicture}%
}
\end{center}

% 改：
\begin{figure}[H]
\centering
\resizebox{\textwidth}{!}{%
\begin{tikzpicture}[...]
...
\end{tikzpicture}%
}
\caption{<caption>}\label{<label>}
\end{figure}
```

**关键约束**：caption/label 必须在 resizebox 的 `}` 之后、`\end{figure}` 之前——放在 resizebox 内部会导致编译错误。

**编译器路径**：`C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe`

## Todos

- [x] 1. 第 0-1 章 TikZ 图补 figure 环境 + caption + label + \ref（#1-5：行 250、312、494、580、694）
  - References: 逐图替换表 #1-#5；行 247-710
  - 改造 5 处 `\begin{center}...\end{center}` → `\begin{figure}[H]\centering...\caption{...}\label{...}\end{figure}`
  - #1 非 resizebox：行 249 `\begin{center}`→`\begin{figure}[H]\centering`，行 279 `\end{center}`→`\caption{Git、GitHub 与 VS Code 的关系——三者协作构成完整的版本控制工作流}\label{fig:three-analogies}\end{figure}`
  - #2 非 resizebox：行 311 → `\begin{figure}[H]\centering`，行 331 → `\caption{本教程学习路径——从心智模型到实战工作流的章节安排}\label{fig:learning-path}\end{figure}`
  - #3 非 resizebox：行 493 → `\begin{figure}[H]\centering`，行 509 → `\caption{Git 的四个核心对象——Blob、Tree、Commit、Branch 的层次关系}\label{fig:git-objects}\end{figure}`
  - #4 resizebox：行 578 → `\begin{figure}[H]\centering`，行 620 → `\caption{一次提交的完整数据模型——Commit 指向 Tree，Tree 指向 Blob，并记录父提交}\label{fig:commit-structure}\end{figure}`
  - #5 非 resizebox：行 693 → `\begin{figure}[H]\centering`，行 710 → `\caption{Git 三大工作区域与远程仓库——工作区、暂存区、本地仓库、远程仓库之间的数据流转}\label{fig:three-zones}\end{figure}`
  - 正文 \ref 替换 5 处：行 247 后插句、行 309 后插句、行 491 改句、行 576 改句、行 691 改句（按替换表 #1-#5 原文→目标）
  - Acceptance: 5 处 `\begin{figure}[H]` 新增；5 处 `\label{fig:` 新增；5 处 `\ref{fig:` 新增；原"下图展示了"在 576 行消失
  - QA happy: grep `\\begin\{figure\}\[H\]` 新增 5 处（对照现有 3 处命令定义）；grep `{fig:three-analogies}|{fig:learning-path}|{fig:git-objects}|{fig:commit-structure}|{fig:three-zones}` 各命中 1 处 label + 1 处 ref；证据 `.omo/evidence/t1-grep.txt`。**不编译**
  - QA failure: 某 label 未命中 → 回查替换表 #1-#5；`\begin{center}` 残留 → 回查对应行
  - Commit: `第 0-1 章 TikZ 图补 figure 环境 + caption + label + 交叉引用`

- [x] 2. 第 3-5 章 TikZ 图补 figure 环境 + caption + label + \ref（#6-8：行 1189、1832、2211）
  - References: 逐图替换表 #6-#8；行 1186-2233
  - 改造 3 处 `\begin{center}...\end{center}` → `\begin{figure}[H]\centering...\caption{...}\label{...}\end{figure}`
  - #6 非 resizebox：行 1188 → `\begin{figure}[H]\centering`，行 1213 → `\caption{文件状态生命周期——未跟踪、已修改、已暂存、未修改之间的转换关系}\label{fig:file-states}\end{figure}`
  - #7 resizebox：行 1830 → `\begin{figure}[H]\centering`，行 1865 → `\caption{Merge 与 Rebase 的对比——Merge 保留完整历史产生合并节点，Rebase 生成线性历史}\label{fig:merge-rebase}\end{figure}`
  - #8 非 resizebox：行 2210 → `\begin{figure}[H]\centering`，行 2233 → `\caption{Pull Request 完整工作流程——从创建分支到代码审查再到合并的协作循环}\label{fig:pr-workflow}\end{figure}`
  - 正文 \ref 替换 3 处：行 1186 改句、行 1828 后插句、行 2208 改句（按替换表 #6-#8 原文→目标）
  - Acceptance: 3 处 `\begin{figure}[H]` 新增；3 处 `\label{fig:` 新增；3 处 `\ref{fig:` 新增；原"下图展示了"在 2208 行消失
  - QA happy: grep `{fig:file-states}|{fig:merge-rebase}|{fig:pr-workflow}` 各命中 1 处 label + 1 处 ref；证据 `.omo/evidence/t2-grep.txt`。**不编译**
  - QA failure: #7 resizebox 图 `}` 闭合错误 → 回查行 1864-1865 转换
  - Commit: `第 3-5 章 TikZ 图补 figure 环境 + caption + label + 交叉引用`

- [x] 3. 第 9 章 TikZ 图补 figure 环境 + caption + label + \ref（#9-11：行 2778、2836、2901）+ 首次编译验证
  - References: 逐图替换表 #9-#11；行 2774-2972
  - 改造 3 处 `\begin{center}...\end{center}` → `\begin{figure}[H]\centering...\caption{...}\label{...}\end{figure}`（全部 resizebox）
  - #9：行 2776 → `\begin{figure}[H]\centering`，行 2828 → `\caption{Git 四大区域数据流转速查——工作区、暂存区、本地仓库、远程仓库之间的完整操作路径}\label{fig:cheatsheet-zones}\end{figure}`
  - #10：行 2834 → `\begin{figure}[H]\centering`，行 2888 → `\caption{分支演变对比速查——Merge 产生菱形合并节点，Rebase 生成线性历史}\label{fig:cheatsheet-merge-rebase}\end{figure}`
  - #11：行 2899 → `\begin{figure}[H]\centering`，行 2972 → `\caption{远程协作完整链路速查——从本地开发到 GitHub 合并的完整流程}\label{fig:cheatsheet-remote}\end{figure}`
  - 正文 \ref 替换 3 处：行 2774 后插句、行 2832 后插句、行 2897 后插句（按替换表 #9-#11 原文→目标）
  - **首次编译**：全部 11 处更新完成后，运行 `C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex` 两次
  - Acceptance: 3 处 `\begin{figure}[H]` 新增；3 处 `\label{fig:` 新增；3 处 `\ref{fig:` 新增；xelatex 两次无致命错误；编译日志无 `Reference undefined`、无 `multiply defined`
  - QA happy: grep `{fig:cheatsheet-zones}|{fig:cheatsheet-merge-rebase}|{fig:cheatsheet-remote}` 各命中；grep `\begin{figure}\[H\]` 新增 11 处（对照现有 3 处命令定义=14 总）；编译日志无 undefined/multiply；证据 `.omo/evidence/t3-grep.txt` + `.omo/evidence/t3-xelatex-log-1.txt` + `.omo/evidence/t3-xelatex-log-2.txt`
  - QA failure: `Reference undefined` → label/\ref 拼写校对（回查替换表 #9-#11）；`multiply defined` → label 键与现有 26 处位图撞名（回查 fig: 命名表）；resizebox `}` 闭合错误 → 回查行 2826-2828、2886-2888、2970-2972
  - Commit: `第 9 章 TikZ 图补 figure 环境 + caption + label + 交叉引用 + 首次编译验证`

## Final verification wave

- [x] F1. 计划合规审计
  - 校验：grep `\begin{figure}\[H\]` 新增 11 处；grep `\label{fig:` 新增 11 处（TikZ 专用，与现有 26 处位图 label 不冲突）；grep `\ref{fig:` 新增 11 处；11 个 label 键全部唯一不与现有 26 个位图 label 撞名
  - 证据：`.omo/evidence/f1-compliance-grep.txt`
- [x] F2. LaTeX 编译与引用一致性审查
  - xelatex 两次最终日志无致命错误、无 `Reference undefined`、无 `multiply defined`；用 `look_at` 工具读取 `Git_VSCode_Tutorial.pdf` 抽查 3 页，确认 TikZ 图标题渲染为"图 N"且正文 `\ref` 渲染为正确编号
  - 证据：`.omo/evidence/f2-xelatex-final-log.txt` + `.omo/evidence/f2-pdf-lookat.txt`
- [x] F3. 实际 QA（PDF 翻阅）
  - 用 `look_at` 工具逐章翻阅 PDF，确认 11 个 TikZ 图均有"图 N：xxx"标题；正文每处"如图~\ref{} 所示"渲染为正确图号；现有 26 处位图编号因新增图重排后仍正确引用
  - 证据：`.omo/evidence/f3-pdf-qa-checklist.md`
- [x] F4. 范围保真审计
  - git diff 确认未改 TikZ 图内容（节点/箭头/样式定义零改动）；仅改 11 处 `\begin{center}→\begin{figure}[H]` + 11 处 `\end{center}→\caption+\label+\end{figure}` + 11 处引导句 + 6 处新插句；未改现有 26 处位图；未引新包；未改 tcolorbox；未改 images/；未改 sc-*.md
  - 证据：`.omo/evidence/f4-scope-fidelity-git-diff.txt`

## Commit strategy

原子提交，每个 todo 一个 commit，中文消息。T1-T2 commit 时不编译，仅 grep 校验后提交；T3 commit 含首次编译验证结果。F1-F4 不产生 commit。

## Success criteria

- 11 个 TikZ 图均有 `\begin{figure}[H]` + `\caption` + `\label`，键名与逐图替换表一一对应
- 正文每处引导句含 `\ref{fig:xxx}` 交叉引用，PDF 渲染为"图 N"
- 新增 11 个 label 键全部唯一，不与现有 26 个位图 label 撞名
- xelatex 两次编译无致命错误、无 undefined reference、无 multiply defined
- TikZ 图内容零改动（节点/箭头/样式定义不变）；现有 26 处位图零改动；零新依赖
- 现有 26 处位图的 `\ref` 在图编号重排后仍渲染正确（两次编译自动解析）
