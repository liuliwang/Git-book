# agents-md-doc-requirements - Work Plan

## TL;DR (For humans)
<!-- Fill this LAST, after the detailed plan below is written, so it summarizes the REAL plan. -->
<!-- Plain English for a non-engineer: NO file paths, NO todo numbers, NO wave/agent/tool names. -->

**What you'll get:** 一份中文项目说明文件（AGENTS.md），写清楚“这份 Git 教程文档应当如何撰写”的完整规范：写作要求（受众与语言、章节结构、配图规则、命令准确性、术语统一、质量门槛、安全边界）以及项目的编译方式与文件结构，供 AI 助手今后修改文档时遵循。

**Why this approach:** 以写作要求为主体、辅以项目技术信息，是官方规范与数千仓库实践验证的有效结构；所有内容均取自项目真实文件，不凭空编写，且含可执行的编译/校验命令与三级边界。

**What it will NOT do:** 不改动教程正文、PDF、图片或参考文档；不创建任何其他文件；不含 git 提交规范（当前目录不是 git 仓库）。

**Effort:** Short
**Risk:** Low - 单一文档文件创建，只读引用现有事实，不触碰任何现有产物

**Decisions to sanity-check:** 文件范围（撰写要求为核心+技术约定，用户已确认）；语言（中文，技术标识符英文）；篇幅（目标 ≤150 行、硬上限 300）；三级边界（Always/Ask first/Never）；不含 git 提交约定（目录非 git 仓库）

Your next move: 批准执行（`$start-work agents-md-doc-requirements`），或先运行高精度评审。执行细节见下文。

---

> TL;DR (machine): Short / Low / 交付根目录 AGENTS.md（文档撰写要求为核心+技术约定支撑，8 区块、中文、≤300 行），全程不改动现有 .tex/PDF/图片/参考文档。

## Scope
### Must have
- 新建 `C:\Users\Admin\Desktop\Git\AGENTS.md`（唯一交付文件）
- 区块（顺序）：① 项目概览（1-2 行）② 构建/编译命令（完整 xelatex 绝对路径 + 编译两次说明）③ 项目结构（表：.tex / images/ / sc-*.md / .omo/）④ **文档撰写基本要求**（核心专题，见下）⑤ LaTeX 技术约定（5 个 tcolorbox 环境 + 3 个图片命令 + listings shell 样式）⑥ 参考文档（sc-*.md 只读说明）⑦ 三级边界（Always / Ask first / Never）⑧ 备注
- "文档撰写基本要求"专题逐条为"可执行规则 + 正反例"，至少覆盖 7 项：受众与语言（中文正文，Git 术语首现附英文，面向初学者）；章节结构规范（固定 7 章+附录，操作用 stepbox、概念用 keybox、警示用 warnbox）；图文并茂（关键操作必须配图；图片只能引用 images/ 中真实存在的文件；用 vscodeimg/vscodeimgnarrow/vscodeimgwide 之一嵌入且必须带 caption）；命令准确性（命令必须真实可执行、复制即用；shell 代码用 listings shell 样式）；术语一致性（全文统一如"暂存区/stage"，不混用译法）；质量门槛（修改后必须 xelatex 编译两次通过、无致命错误；新截图命名 NN-description.png 且入 images/）；安全边界（绝不出现真实密钥/令牌；绝不伪造截图）
- 事实证据文件 `.omo/evidence/task-1-agents-md-doc-requirements.md`（盘点结果，供后续 todo 引用）
- 全文中文、技术标识符/命令英文；目标 ≤150 行，硬上限 300 行

### Must NOT have (guardrails, anti-slop, scope boundaries)
- 不得修改 `Git_VSCode_Tutorial.tex`、`Git_VSCode_Tutorial.pdf`、`images/*`、`sc-*.md`、`.omo/` 内已有文件（证据文件除外）
- 不得创建除 AGENTS.md 外的任何项目文件
- 不包含 Git 提交/PR/分支规范（目录非 git 仓库，无意义）
- 不复制/import 全局 `~/.config/opencode/AGENTS.md` 内容（独立成文）
- 不包含无依据的虚构约定（所有命令/环境/路径必须来自真实文件）
- 篇幅不超过 300 行

## Verification strategy
> Zero human intervention - all verification is agent-executed.
- Test decision: none（文档文件，无代码测试）+ 逐项事实交叉校验
- Evidence: `.omo/evidence/task-<N>-agents-md-doc-requirements.md`（非 ulw-loop 环境，用 `.omo/evidence/`）
- 校验手段：grep 对照 .tex 定义、filesystem 检查图片/路径存在、运行 `xelatex --version` 验证编译命令可执行
- 环境为 win32：行数统计用 PowerShell `(Get-Content AGENTS.md).Count`；文本检索可用本工具 grep 或 PowerShell `Select-String`（同语义），勿假设 PATH 中存在 wc/grep

## Execution strategy
### Parallel execution waves
- Wave 1: Todo 1（盘点，串行前置，产出证据文件）
- Wave 2: Todo 2（撰写 AGENTS.md，依赖 Todo 1）
- Wave 3: Todo 3（交叉校验，依赖 Todo 1+2）
- Final wave: F1-F4 并行

### Dependency matrix
| Todo | Depends on | Blocks | Can parallelize with |
| --- | --- | --- | --- |
| 1 | - | 2, 3 | - |
| 2 | 1 | 3 | - |
| 3 | 1, 2 | - | - |
| F1-F4 | 3 | - | F1-F4 相互 |

## Todos
> Implementation + Test = ONE todo. Never separate.
<!-- APPEND TASK BATCHES BELOW THIS LINE WITH edit/apply_patch - never rewrite the headers above. -->

- [x] 1. 盘点项目事实，产出证据文件
  What to do / Must NOT do: 用 grep 读取 `C:\Users\Admin\Desktop\Git\Git_VSCode_Tutorial.tex` 前 160 行，提取：① 5 个自定义 tcolorbox 环境名（tipbox/warnbox/infobox/keybox/stepbox，.tex:89-133）；② 3 个图片命令名及宽度（\vscodeimg 0.92 / \vscodeimgnarrow 0.72 / \vscodeimgwide，.tex:135-160）；③ listings 样式（shell，.tex:57-87）；④ 编译首行（`% !TEX program = xelatex`，.tex:1）；⑤ 章节结构（grep `\chapter` 获取 7 章+附录标题）。列出 `images/` 目录全部文件（应为 27 张 PNG）。确认 xelatex 路径 `C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe` 存在（运行 `dir` 或 `--version`）。确认 4 份 sc-*.md 存在。将以上全部写入 `.omo/evidence/task-1-agents-md-doc-requirements.md`。不得修改任何现有文件。若某事实与上述不符，以真实文件为准并记录差异。
  Parallelization: Wave 1 | Blocked by: - | Blocks: 2, 3
  References (executor has NO interview context - be exhaustive): `C:\Users\Admin\Desktop\Git\Git_VSCode_Tutorial.tex:1-160`（grep `\\(newtcolorbox|newcommand)` 与 `\chapter`）；`C:\Users\Admin\Desktop\Git\images\`（27 文件清单见目录列表）；`C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe`；`C:\Users\Admin\Desktop\Git\sc-overview.md`、`sc-quickstart.md`、`sc-staging.md`、`sc-branches.md`
  Acceptance criteria (agent-executable): `.omo/evidence/task-1-agents-md-doc-requirements.md` 存在且包含：环境名列表 ≥5 项且每项在 .tex 有定义、图片命令 3 项、images 计数 == 27、xelatex 路径存在性结论、章节标题 ≥7+附录
  QA scenarios (name the exact tool + invocation): happy — `grep -o "newtcolorbox{[a-z]*}" Git_VSCode_Tutorial.tex` 输出与证据文件环境名一致；`filesystem_list_directory(images)` 计数 27；failure — 任一项失配（如环境名拼写错误、图片计数 ≠27）则修正证据文件后重跑
  Commit: N | 无（非 git 仓库）

- [x] 2. 撰写 AGENTS.md 初稿
  What to do / Must NOT do: 在 `C:\Users\Admin\Desktop\Git\AGENTS.md` 新建文件，按 Scope/Must have 规定的 8 区块顺序撰写，区块标题字面采用：`## 项目概览`、`## 构建与编译`、`## 项目结构`、`## 文档撰写基本要求`、`## LaTeX 技术约定`、`## 参考文档`、`## 安全边界`、`## 备注`。所有环境名/命令名/路径必须与任务 1 证据文件一致，不得杜撰。撰写要求专题每条给"规则 + 正反例"；涉及密钥的示例一律用明显占位符（如 `<your-token>`、`ghp_xxxxxxxxxxxxxxxxxxxx`），绝不出现真实凭据。行数统计（含空行，PowerShell `(Get-Content AGENTS.md).Count`）≤300。中文正文、技术标识符英文。不得触碰其他任何文件。
  Parallelization: Wave 2 | Blocked by: 1 | Blocks: 3
  References (executor has NO interview context - be exhaustive): `.omo/evidence/task-1-agents-md-doc-requirements.md`；`C:\Users\Admin\Desktop\Git\Git_VSCode_Tutorial.tex:25-160`（宏包与自定义宏原文）；格式参照 `C:\Users\Admin\.config\opencode\AGENTS.md`（结构：角色语言约定/场景路由/约定/安全边界/备注，≤150 行，仅参照不复制）
  Acceptance criteria (agent-executable): `AGENTS.md` 存在；`grep -c "^## " AGENTS.md` ≥8（或 PowerShell `(Select-String -Path AGENTS.md -Pattern '^## ').Count`）；行数 PowerShell `(Get-Content AGENTS.md).Count` ≤300；`grep "stepbox" AGENTS.md` 等 5 个环境名 + 3 个图片命令名全部出现；无引用不存在路径
  QA scenarios (name the exact tool + invocation): happy — 逐区块核对：`grep -E "tipbox|warnbox|infobox|keybox|stepbox|vscodeimg|vscodeimgnarrow|vscodeimgwide" AGENTS.md` 全部命中；行数 PowerShell `(Get-Content AGENTS.md).Count` ≤300；failure — 若发现虚构环境名/路径（对照证据文件 grep 反向核对），改写该条直至一致
  Commit: N | 无（非 git 仓库）

- [x] 3. 交叉校验 AGENTS.md 全部事实引用
  What to do / Must NOT do: 对 AGENTS.md 逐项执行校验并记录到 `.omo/evidence/task-3-agents-md-doc-requirements.md`（PASS/FAIL 清单）：① 5 个环境名与 3 个图片命令名在 .tex 有定义（grep 对照）；② 编译命令路径真实存在（运行 xelatex `--version`，预期输出 MiKTeX 版本信息）；③ AGENTS.md 中出现的任何 images/ 文件名在目录中存在；④ 章节结构描述与 .tex `\chapter` 一致；⑤ 全文无高熵疑似密钥串（已知前缀后跟 ≥20 位字母数字，如 ghp_ 后 32 位十六进制风格）；允许文档化占位符示例（如 `<your-token>`）；⑥ 行数 ≤300。任何 FAIL 必须修正 AGENTS.md 后复检直至全 PASS。不得改动其他文件。
  Parallelization: Wave 3 | Blocked by: 1, 2 | Blocks: -
  References (executor has NO interview context - be exhaustive): `C:\Users\Admin\Desktop\Git\AGENTS.md`；`C:\Users\Admin\Desktop\Git\Git_VSCode_Tutorial.tex:1-160`；`C:\Users\Admin\Desktop\Git\images\`；`C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe`
  Acceptance criteria (agent-executable): `.omo/evidence/task-3-agents-md-doc-requirements.md` 存在且全部 6 项 == PASS；`xelatex.exe --version` 退出码 0；行数检查用 PowerShell `(Get-Content AGENTS.md).Count` ≤300
  QA scenarios (name the exact tool + invocation): happy — 6 项逐项 PASS 且证据文件记录；failure — 任一 FAIL（如图片文件名不存在、行数超限）修正后复检，证据文件记录修正前后对比
  Commit: N | 无（非 git 仓库）

## Final verification wave
> Runs in parallel after ALL todos. ALL must APPROVE. Surface results and wait for the user's explicit okay before declaring complete.
- [x] F1. Plan compliance audit
- [x] F2. Code quality review
- [x] F3. Real manual QA
- [x] F4. Scope fidelity

## Commit strategy
- 目录非 git 仓库（env 确认），不执行任何 git 操作；交付物仅为 AGENTS.md 与 .omo/evidence/ 下的证据文件

## Success criteria
- `C:\Users\Admin\Desktop\Git\AGENTS.md` 存在，8 区块齐全，篇幅 ≤300 行（目标 ≤150）
- 文档撰写基本要求专题完整覆盖 7 项（受众语言/章节结构/图文并茂/命令准确性/术语一致/质量门槛/安全边界），每条为可执行规则 + 正反例
- 全部事实引用与真实文件一致（任务 3 六项校验全 PASS，证据文件留存）
- 除 AGENTS.md 与 .omo/evidence/ 新文件外，项目内零改动
- 撰写要求中体现"图文并茂"：任何图片引用示例指向 images/ 真实存在的文件
