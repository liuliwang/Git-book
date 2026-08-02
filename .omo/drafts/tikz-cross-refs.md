# tikz-cross-refs - Planning Draft

## Intent
- intent: clear
- review_required: false
- slug: tikz-cross-refs
- request: 对文档全部 TikZ 示意图添加图名（caption + label）和正文引用（ref）

## Exploration findings (verified, 2026-08-02)
- 11 个 `\begin{tikzpicture}` 环境全部在 `\begin{center}...\end{center}` 内（非 figure 环境），无 `\caption`、`\label`、`\ref`
- 5 个用 `\resizebox{\textwidth}{!}{%...\end{tikzpicture}%}` 包裹（#4 行580、#7 行1832、#9 行2778、#10 行2836、#11 行2901）
- 6 个无 `\resizebox`（#1 行250、#2 行312、#3 行494、#5 行694、#6 行1189、#8 行2211）
- `float` 包已加载（行 25），`graphicx`（行 9），`tikz`（行 19）——`\begin{figure}[H]` 可用
- 引导句现状：
  - 有"下图展示了"引导：#4（行576）、#8（行2208）
  - 有"："引导：#3（行491"以下四种对象："）、#5（行691"请务必理解："）、#6（行1186"生命周期如下："）
  - 仅有节标题无引导句：#1（行247 \section）、#2（行309 \section）、#7（行1828 \subsection）、#9（行2774 \section）、#10（行2832 \section）、#11（行2897 \section）

## Per-diagram plan (11 items)

| # | 行号 | label 键 | caption | \ref 插入 |
|---|------|----------|---------|----------|
| 1 | 250 | fig:three-analogies | Git、GitHub 与 VS Code 的关系——三者协作构成完整的版本控制工作流 | 行247 \section 后插"三者关系如图~\ref{fig:three-analogies} 所示——" |
| 2 | 312 | fig:learning-path | 本教程学习路径——从心智模型到实战工作流的章节安排 | 行309 \section 后插"完整路径如图~\ref{fig:learning-path} 所示——" |
| 3 | 494 | fig:git-objects | Git 的四个核心对象——Blob、Tree、Commit、Branch 的层次关系 | 行491"以下四种对象："→"以下四种对象，如图~\ref{fig:git-objects} 所示：" |
| 4 | 580 | fig:commit-structure | 一次提交的完整数据模型——Commit 指向 Tree，Tree 指向 Blob，并记录父提交 | 行576"下图展示了一次提交的完整数据模型："→"一次提交的完整数据模型如图~\ref{fig:commit-structure} 所示：" |
| 5 | 694 | fig:three-zones | Git 三大工作区域与远程仓库——工作区、暂存区、本地仓库、远程仓库之间的数据流转 | 行691"请务必理解："→"请务必理解，如图~\ref{fig:three-zones} 所示：" |
| 6 | 1189 | fig:file-states | 文件状态生命周期——未跟踪、已修改、已暂存、未修改之间的转换关系 | 行1186"文件状态的生命周期如下："→"文件状态的生命周期如图~\ref{fig:file-states} 所示：" |
| 7 | 1832 | fig:merge-rebase | Merge 与 Rebase 的对比——Merge 保留完整历史产生合并节点，Rebase 生成线性历史 | 行1828 \subsection 后插"两者对比如图~\ref{fig:merge-rebase} 所示——" |
| 8 | 2211 | fig:pr-workflow | Pull Request 完整工作流程——从创建分支到代码审查再到合并的协作循环 | 行2208"下图展示了 Pull Request 的完整工作流程："→"Pull Request 的完整工作流程如图~\ref{fig:pr-workflow} 所示：" |
| 9 | 2778 | fig:cheatsheet-zones | Git 四大区域数据流转速查——工作区、暂存区、本地仓库、远程仓库之间的完整操作路径 | 行2774 \section 后插"完整数据流转如图~\ref{fig:cheatsheet-zones} 所示——" |
| 10 | 2836 | fig:cheatsheet-merge-rebase | 分支演变对比速查——Merge 产生菱形合并节点，Rebase 生成线性历史 | 行2832 \section 后插"分支演变对比如图~\ref{fig:cheatsheet-merge-rebase} 所示——" |
| 11 | 2901 | fig:cheatsheet-remote | 远程协作完整链路速查——从本地开发到 GitHub 合并的完整流程 | 行2897 \section 后插"完整协作链路如图~\ref{fig:cheatsheet-remote} 所示——" |

## Wrapper conversion pattern

**非 resizebox 图（#1,2,3,5,6,8）：**
```latex
% 原：
\begin{center}
\begin{tikzpicture}...
\end{tikzpicture}
\end{center}
% 改：
\begin{figure}[H]
\centering
\begin{tikzpicture}...
\end{tikzpicture}
\caption{...}\label{fig:...}
\end{figure}
```

**resizebox 图（#4,7,9,10,11）：**
```latex
% 原：
\begin{center}
\resizebox{\textwidth}{!}{%
\begin{tikzpicture}...
\end{tikzpicture}%
}
\end{center}
% 改：
\begin{figure}[H]
\centering
\resizebox{\textwidth}{!}{%
\begin{tikzpicture}...
\end{tikzpicture}%
}
\caption{...}\label{fig:...}
\end{figure}
```

## Approach (decision-complete)
1. 11 个 TikZ 图：`\begin{center}`→`\begin{figure}[H]\centering`，`\end{center}`→`\caption{...}\label{...}\end{figure}`
2. resizebox 图：caption/label 放在 resizebox 的 `}` 之后、`\end{figure}` 之前
3. 11 处正文 \ref 插入（按上表）
4. 全部编辑完成后 xelatex 两次编译验证

## Scope
- IN: Git_VSCode_Tutorial.tex 唯一文件；11 处 TikZ 图包裹 figure 环境 + caption + label + 正文 \ref
- OUT: 不改 TikZ 图内容；不改现有 26 处 \vscodeimg 图；不引入新包；不改 tcolorbox 环境；不改 sc-*.md

## Test strategy
- agent-executed QA：grep 校验 11 处 `\begin{figure}[H]` 新增 + 11 处 `\label{fig:` + 11 处 `\ref{fig:` + xelatex 两次无致命错误
- 编译策略：T1-T3 编辑期不编译（grep 校验），T3 末首次编译
- 图编号变化说明：新增 11 个 figure 会改变现有 26 个图的编号，但 \ref 自动解析，两次编译后正确

## Status
- phase: handoff
- status: plan-generated
- plan: .omo/plans/tikz-cross-refs.md（含 11 行逐图替换表 + wrapper 转换模式 + Todos T1-T3 + F1-F4 + TL;DR 已填）
- self-check: 7 task rows（T1-T3 + F1-F4）column-zero grammar 通过
- next: 呈现 handoff（CLEAR, review_required=false → 询问"开始执行 via /start-work tikz-cross-refs，或先跑双高精度审查"）
