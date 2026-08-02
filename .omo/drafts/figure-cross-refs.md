# figure-cross-refs - Planning Draft

## Intent
- intent: clear
- review_required: false
- slug: figure-cross-refs
- request: 给文档中图片添加名称，在正文中提供对应引用和说明

## Exploration findings (verified, 2026-08-02)
- 文档：C:\Users\Admin\Desktop\Git\Git_VSCode_Tutorial.tex（3113 行，项目唯一 .tex 源）
- 图片命令定义（行 132-154）：
  - `\vscodeimg{路径}{caption}`     -> figure[H] + \includegraphics[width=0.92\textwidth] + \caption
  - `\vscodeimgnarrow{路径}{caption}` -> width=0.72\textwidth
  - `\vscodeimgwide{路径}{caption}`  -> width=\textwidth
  - 三者命令体均只有 \caption，**无 \label**
- 正文引用现状：grep `\ref|\autoref|\cref|\eqref|\listoffigures|\figurename` -> **零匹配**；正文用"下图展示了"、"如下——"自然语言指代，无正式"图 N"交叉引用
- caption 现状：26 处正文调用全部提供 caption 文字，已是「名称——说明」格式
- 图片调用清单（行号 -> 命令 -> 文件）：
  - 737  \vscodeimg     images/01-sc-overview.png
  - 1134 \vscodeimg     images/12-init-repo.png
  - 1156 \vscodeimg     images/11-clone-repo.png
  - 1235 \vscodeimg     images/13-modified-files.png        (复用 #1)
  - 1273 \vscodeimg     images/05-stage-changes.png
  - 1296 \vscodeimg     images/15-commit-button.png
  - 1335 \vscodeimg     images/03-diff-editor.png
  - 1351 \vscodeimgnarrow images/24-diff-sidebyside.png     (minipage 并排)
  - 1355 \vscodeimgnarrow images/25-diff-inline.png          (minipage 并排)
  - 1363 \vscodeimg     images/21-gutter-diff.png
  - 1691 \vscodeimg     images/18-current-branch.png
  - 1695 \vscodeimg     images/19-create-branch.png         (复用 #1)
  - 1774 \vscodeimg     images/merge-conflict.png
  - 1789 \vscodeimg     images/08-merge-editor.png
  - 2098 \vscodeimg     images/17-pull-push.png
  - 2100 \vscodeimg     images/16-sync-button.png
  - 2138 \vscodeimg     images/13-modified-files.png        (复用 #2, caption=一键发布)
  - 2315 \vscodeimgwide images/01-sc-overview.png           (复用 #2, caption=A/B/C/D/E 标注)
  - 2341 \vscodeimg     images/02-sc-graph.png
  - 2347 \vscodeimg     images/22-stage-detail.png
  - 2353 \vscodeimg     images/23-stage-lines.png
  - 2371 \vscodeimg     images/07-sync-changes.png
  - 2379 \vscodeimg     images/19-create-branch.png         (复用 #2)
  - 2385 \vscodeimg     images/10-timeline.png
  - 2409 \vscodeimg     images/04-gutter-blame.png
  - 2542 \vscodeimg     images/20-view-changes.png
- 重复复用（caption 语义不同，各为独立 figure）：
  - 01-sc-overview.png: 737(全景) + 2315(A/B/C/D/E 详解)
  - 13-modified-files.png: 1235(修改列表) + 2138(一键发布)
  - 19-create-branch.png: 1695 + 2379（措辞略异）
- minipage 并排：1351/1355 在 \begin{minipage}[t]{0.48\textwidth} 内并排
- images/ 目录 27 张 png（01-26 + merge-conflict.png），全部被引用
- 项目约束（AGENTS.md）：xelatex 编译两次；不得新增 tcolorbox 环境类型；图片只引用 images/ 真实文件；Shell 用 listings；首行 `% !TEX program = xelatex`；最小改动约定

## Components ledger (topology lock)
| id | outcome | status | evidence |
|----|---------|--------|----------|
| C1 | 图片 label 体系：每个 figure 加 \label，使"图 N"可被 \ref 引用 | planned | 行 132-154 命令体 |
| C2 | 正文交叉引用：自然指代处插入 \ref + 衔接说明 | planned | 26 处调用上下文 |
| C3 | caption 处理 | decided | 用户选 F1=保留+加label |
| C4 | 编号格式配置（\figurename 中文化） | planned=default | D3 |
| C5 | 重复图片 label 策略 | decided | 用户选 F2=独立label |

## Adopted defaults
- D1 label 命名=语义名 `fig:<短语义>`；复用图加语义后缀（fig:sc-overview / fig:sc-overview-annotated / fig:modified-files / fig:modified-files-publish / fig:create-branch / fig:create-branch-alt）
- D2 正文措辞=保留原过渡句，在自然指代处插入"如图~\ref{fig:xxx} 所示"或"（图~\ref{fig:xxx}）"，不删现有说明
- D3 编号格式=确认/添加 `\renewcommand{\figurename}{图}`；caption 分隔符沿用 LaTeX 默认；不引入 caption 包
- D4 不新增 \listoffigures
- D5 编译=每批改动后 xelatex 两次，无致命错误即通过
- D6 QA=agent 执行：xelatex 两次通过 + grep 校验 \label 数=26、\ref 无悬空、调用与 label 一一对应

## Decisions (user-resolved forks)
- F1 caption 处理 = 保留现有 caption 原样 + 每个 figure 加 \label（最小改动）
- F2 重复图片 label = 每处独立 label（复用图加语义后缀），每张图独立编号可引

## Approach (planned, decision-complete)
1. 改造三个图片命令定义（行 132-154）：`\caption{#2}` 后追加 `\label{#3}`，命令签名由 `[2]` 改为 `[3]`，第 3 参数=label 键。即 `\vscodeimg{路径}{caption}{labelkey}`。三命令同步改造
2. 更新 26 处正文调用，每处补第 3 参数 label 键（语义名；复用图加后缀）
3. 在每处图片调用的引导/衔接句中插入 `\ref` 引用（如"下图展示了"->"如图~\ref{fig:sc-overview} 所示"），保留原说明文字
4. 导言区确认/添加 `\renewcommand{\figurename}{图}`（若未配置）；位置在行 86-129 tcolorbox 定义之后、行 132 图片命令之前
5. 每批改动后 xelatex 两次编译验证（C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe）
6. grep 一致性校验：`\label{fig:` 计数=26、`\ref{fig:` 无悬空（每个 \ref 必有对应 \label）、调用与 label 一一对应

## Scope
- IN: Git_VSCode_Tutorial.tex 唯一文件；改图片命令定义 + 26 处调用补 label 键 + 正文插入 \ref + 导言区加 \figurename 配置
- OUT: 不改 caption 文字内容；不改 tcolorbox 环境与图标；不改 images/ 图片；不加 \listoffigures；不改章节结构；不引入新依赖；不改 sc-*.md 参考文档

## Test strategy
- agent-executed QA（D6）：xelatex 两次编译无致命错误 + grep 一致性校验
- 无 TDD（文档项目，编译通过+引用一致即验收）
- 失败 QA 场景：编译报 `LaTeX Warning: Reference ... undefined` -> 说明有悬空 \ref，回查 label 拼写；编译报 `! Argument of \vscodeimg has an extra }` -> 第 3 参数缺失或括号不匹配

## Status
- phase: handoff
- status: plan-generated
- plan: .omo/plans/figure-cross-refs.md（含逐处替换表 26 行、Todos T1-T7、Final verification F1-F4、TL;DR 已填、Metis findings 全 fold）
- metis-review: ses_03da894f2ffeT3OQes1dRY7MqS（findings B1/M1/M2/M3/m1-m5 全部 fold 入 m0016 重写计划）
- self-check: 11 task rows（T1-T7 + F1-F4）column-zero grammar 通过
- next: 呈现 handoff（CLEAR, review_required=false → 询问"开始执行 via /start-work figure-cross-refs，或先跑双高精度审查"）
