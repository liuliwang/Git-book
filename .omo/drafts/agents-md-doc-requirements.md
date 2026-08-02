---
slug: agents-md-doc-requirements
status: awaiting-approval
intent: clear
review_required: false
pending-action: write .omo/plans/agents-md-doc-requirements.md
approach: 在项目根目录创建 AGENTS.md，以"文档撰写基本要求"为核心内容（面向本 LaTeX 教程的写作规范：受众/结构/图文/命令准确性/术语/质量门槛/安全边界），并辅以 AGENTS.md 规范要求的支撑性技术约定（项目概览、构建命令、项目结构、LaTeX 约定、参考文档、三级边界），全文中文、目标 ≤150 行，逐项与真实 .tex/图片/编译命令交叉校验后交付。
---

# Draft: agents-md-doc-requirements

## Components (topology ledger)
<!-- id | outcome (one line) | status: active|deferred | evidence path -->
<!-- c1 | 文档撰写要求清单（核心交付内容）| active | 用户请求 + AGENTS.md 最佳实践研究 -->
<!-- c2 | 支撑性技术约定（概览/构建/结构/LaTeX 约定/边界）| active | agents.md 规范六核心区（commands/structure/style/boundaries） -->
<!-- c3 | 事实准确性校验（所有引用的环境/命令/图片/路径与真实文件一致）| active | Git_VSCode_Tutorial.tex:25-160, images/ 27 张, xelatex 路径 -->

## Open assumptions (announced defaults)
<!-- assumption | adopted default | rationale | reversible? -->
<!-- 文件范围 | 撰写要求为核心 + 支撑性技术约定 | AGENTS.md 最佳实践：有效文件必备可执行命令/结构/边界；用户已确认核心为撰写要求 | 是（简报中提问，跳过则采用此默认） -->
<!-- 语言 | 中文（技术标识符英文）| 用户中文交流，与全局 AGENTS.md 一致 | 是 -->
<!-- 篇幅 | 目标 ≤150 行 | 实践共识 60-300 行；全局 AGENTS.md 同约束 | 是 -->
<!-- 编译验证 | 每次修改 .tex 后必须 xelatex 编译两次通过 | 项目既有构建事实（历史 3 次成功编译） | 是 -->
<!-- Git 工作流约定 | 不包含（目录非 git 仓库）| env 确认 Is directory a git repo: no | 是 -->
<!-- 参考文档 sc-*.md | 列为只读参考，可引用不可改写 | 官方文档抓取件，保持原文 | 是 -->

## Findings (cited - path:lines)
- 项目根目录不存在 AGENTS.md（filesystem 读取 ENOENT）；全局规则位于 ~/.config/opencode/AGENTS.md（格式参照：语言约定/场景路由/约定/边界/备注，≤150 行）
- 主文档 Git_VSCode_Tutorial.tex 为 ctexbook+xelatex：`% !TEX program = xelatex`（.tex:1）；宏包 19 个含 tcolorbox/fontawesome5/tikz(usetikzlibrary positioning)/wrapfig/float/caption（.tex:8-31）
- 5 个自定义 tcolorbox 环境：tipbox/warnbox/infobox/keybox/stepbox（.tex:89-133）
- 3 个图片命令：\vscodeimg（0.92\textwidth）/ \vscodeimgnarrow（0.72）/ \vscodeimgwide（.tex:135-160）
- listings shell 样式 + literate 特殊字符映射（.tex:57-87）；\hypersetup colorlinks（.tex:40-49）
- images/ 共 27 张 PNG（01-sc-overview.png … 26-sc-graph-detail.png + merge-conflict.png）
- 编译命令（历史 3 次成功）：C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex，需编译两次；产出 58 页/3.1MB PDF
- 4 份参考文档 sc-overview/sc-quickstart/sc-staging/sc-branches.md（VS Code 官方文档抓取件）
- 目录非 git 仓库 → 无提交/PR 约定可写

## Decisions (with rationale)
- d1: AGENTS.md 落在项目根目录（C:\Users\Admin\Desktop\Git\AGENTS.md）——agents.md 规范：仓库根目录、最近文件优先
- d2: 结构按"六核心区"（命令/验证/结构/风格/边界）+ 用户核心诉求"文档撰写要求"专题章节 —— GitHub 2500+ 仓库分析结论：有效文件覆盖 commands/testing/structure/style/git/boundaries
- d3: 撰写要求章节逐条给出"可执行规则 + 正反例"而非抽象描述 —— 研究结论：代码/命令示例优于描述性散文；仅列 agent 无法从代码推断的信息
- d4: 三级边界 Always/Ask first/Never —— 研究结论：明确边界是最高频有效约束（"Never commit secrets" 为最常见条目）

## Scope IN
- 创建 C:\Users\Admin\Desktop\Git\AGENTS.md（全新文件）
- 内容：项目概览、构建/编译命令、项目结构、LaTeX 技术约定（5 环境+3 图片命令+listings）、文档撰写基本要求专题、三级边界
- 撰写要求专题至少覆盖：受众与语言、章节结构规范、图文并茂（图片引用规则）、命令准确性、术语一致性、质量门槛（编译两次通过）、安全边界

## Scope OUT (Must NOT have)
- 不改动 Git_VSCode_Tutorial.tex / PDF / images/ / sc-*.md 任何内容
- 不创建除 AGENTS.md 外的任何文件（.omo/ 计划产物除外）
- 不包含 Git 提交/PR 规范（目录非 git 仓库）
- 不复制全局 AGENTS.md 内容（独立成文，不 import）
- 篇幅不超过 300 行（目标 ≤150）

## Open questions
- q1（所有者决策）：AGENTS.md 范围——(A) 仅"文档撰写基本要求"专题；(B) 撰写要求为核心 + 支撑性技术约定（项目概览/构建命令/结构/LaTeX 约定/边界）——推荐 B，理由：研究证实仅靠规则清单缺少可执行命令/结构/边界的 AGENTS.md 对 agent 价值大减，且 B 中撰写要求仍占主导。

## Approval gate
status: approved (2026-08-02)
<!-- 用户已回答 q1（选择：撰写要求+技术约定，Recommended），构成批准。计划已创建于 .omo/plans/agents-md-doc-requirements.md；Metis 子代理两次无输出，已由规划者以自审完成差距分析并折叠 4 项修复（字面区块标题、凭据措辞、高熵密钥校验、win32 命令）；TL;DR 待填 -->
