# figure-cross-refs - Work Plan

## TL;DR (For humans)

为 26 处图片建立正式交叉引用：改造三个图片命令签名 [2]→[3] 追加 `\label`，26 处调用补第 3 参数 label 键，逐处引导句插入 `\ref`，导言区配置 `\figurename`=图。7 个实现 todo 按章节分批（T2-T7），T1-T7 纯编辑不编译（改签名后旧调用会崩溃），T7 末首次 xelatex 两次编译。F1-F4 最终验证（合规 grep / 编译+PDF look_at / 翻阅 / 范围保真 git diff）。3 处复用图各独立 label 不冲突；minipage 并排双图各独立编号。caption 与 tcolorbox 零改动。

## Scope

**IN**：唯一文件 `C:\Users\Admin\Desktop\Git\Git_VSCode_Tutorial.tex`
- 改造三个图片命令定义（行 132-154）：签名 `[2]→[3]`，命令体 `\caption{#2}` 后追加 `\label{#3}`
- 26 处正文图片调用各补第 3 参数 label 键
- 每处图片调用的引导句按"逐处替换表"插入 `\ref{fig:xxx}` 交叉引用，保留原说明文字
- 导言区行 129 之后、132 之前添加 `\renewcommand{\figurename}{图}`（已确认当前文档零 `\figurename`）
- 创建证据目录 `.omo/evidence/`
- 全部 26 处编辑完成后，T7 末尾首次 xelatex 两次编译验证

**OUT**（Must-NOT-Have）：
- 不改任何 caption 文字内容（F1=保留）
- 不改 tcolorbox 环境定义、图标、标签文本（行 86-129）
- 不改 `images/` 目录中任何图片文件
- 不新增 `\listoffigures` 图清单（D4）
- 不改章节结构（第 0-9 章 + 附录）
- 不引入新 LaTeX 包/依赖（含不引入 `xparse`、`caption` 包）
- 不改 `sc-*.md` 参考文档（只读）
- 不改首行 `% !TEX program = xelatex`
- T2-T6 期间**不编译**（改签名后旧调用会报错，编译无意义）

## Verification strategy

agent-executed QA，零人工介入：

1. **编辑期校验（T2-T6）**：grep 校验每处调用含第 3 参数 + 引导句已替换（原"下图"/"如下"指代消失，新 `\ref{fig:}` 出现）。**不编译**——T1 改签名 `[3]` 后，未更新的旧调用（仍 2 参数）会编译报错（minipage 内 1351/1355 的 `#3` 会吞 `\end` 致命崩溃），故编译留到 T7 全部更新后。

2. **首次编译（T7 末）**：`C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex` 两次；无致命错误即通过（Overfull/Underfull 警告允许；首次编译日志中"fatal: refusing to"是正文示例文本非错误）。编译日志须无 `LaTeX Warning: Reference ... undefined`、无 `Label ... multiply defined`。

3. **引用一致性 grep 校验（T7/F1）**：
   - `\label{fig:` 计数 = 26
   - 每个 `\ref{fig:xxx}` 必有同键 `\label{fig:xxx}`（无悬空）
   - 三处复用图 label 各唯一不冲突

4. **失败 QA 场景**：
   - `! Argument of \vscodeimg has an extra }` → 第 3 参数缺失或括号不匹配
   - `Reference ... undefined` → label 拼写与 \ref 不一致
   - `Label ... multiply defined` → 复用图后缀漏加
   - minipage 内 `[H]` float 报错 → 启用 M3 降级方案

无 TDD（文档项目，编译通过 + 引用一致即验收）。

## Execution strategy

**编译策略（B1 修复）**：T1 改签名 → T2-T7 分批编辑（仅 grep 校验，不编译）→ T7 末首次 xelatex 两次编译。原因：`\newcommand{\vscodeimg}[3]` 后旧 2 参数调用会编译崩溃（尤其 minipage 内 `#3` 吞 `\end`），中间编译无意义且误导。

**逐处替换表**（M2/m4 修复，decision-complete，执行者零判断）：

| # | 图行 | label 键 | 引导句行 | 原文 → 目标措辞 |
|---|------|----------|----------|-----------------|
| 1 | 737 | fig:sc-overview | 734 | "下图展示了 VS Code 源代码管理视图" → "如图~\ref{fig:sc-overview} 所示，VS Code 源代码管理视图" |
| 2 | 1134 | fig:init-repo | 1132 | "并显示\"初始化仓库\"按钮：" → "并显示\"初始化仓库\"按钮，如图~\ref{fig:init-repo} 所示：" |
| 3 | 1156 | fig:clone-repo | 1154 | "可以通过命令面板执行克隆操作：" → "可以通过命令面板执行克隆操作，如图~\ref{fig:clone-repo} 所示：" |
| 4 | 1235 | fig:modified-files | 1233 | "直接体现在源代码管理视图里：" → "直接体现在源代码管理视图里，如图~\ref{fig:modified-files} 所示：" |
| 4b | (同图二引用) | 1237 | "上图中，你可以看到：" → "图~\ref{fig:modified-files} 中，你可以看到：" |
| 5 | 1273 | fig:stage-changes | 1271 | "（全部暂存）：" → "（全部暂存），如图~\ref{fig:stage-changes} 所示：" |
| 6 | 1296 | fig:commit-button | 1294 | "输入提交信息 → 点击提交按钮。" → "输入提交信息 → 点击提交按钮，如图~\ref{fig:commit-button} 所示。" |
| 7 | 1335 | fig:diff-editor | 1333 | "即可打开差异对比视图：" → "即可打开差异对比视图，如图~\ref{fig:diff-editor} 所示：" |
| 8 | 1351 | fig:diff-sidebyside | 1346 | "两种模式的对比如下——" → "两种模式的对比如图~\ref{fig:diff-sidebyside} 与图~\ref{fig:diff-inline} 所示——" |
| 9 | 1355 | fig:diff-inline | (同 1346) | (与 #8 同句双引用，见上) |
| 10 | 1363 | fig:gutter-diff | 1361 | "实时显示变更指示：" → "实时显示变更指示，如图~\ref{fig:gutter-diff} 所示：" |
| 11 | 1691 | fig:current-branch | 1689 | "切换或创建新分支：" → "切换或创建新分支，如图~\ref{fig:current-branch} 所示：" |
| 12 | 1695 | fig:create-branch | 1693 | "VS Code 会自动从当前分支创建并切换：" → "VS Code 会自动从当前分支创建并切换，如图~\ref{fig:create-branch} 所示：" |
| 13 | 1774 | fig:merge-conflict | 1764 | "Git 会在文件中插入冲突标记：" → "Git 会在文件中插入冲突标记（如图~\ref{fig:merge-conflict} 所示）：" |
| 14 | 1789 | fig:merge-editor | 1787 | "VS Code 还提供了专门的三方合并编辑器：" → "VS Code 还提供了专门的三方合并编辑器，如图~\ref{fig:merge-editor} 所示：" |
| 15 | 2098 | fig:pull-push | 2096 | "同步按钮一键完成：" → "同步按钮一键完成，如图~\ref{fig:pull-push} 与图~\ref{fig:sync-button} 所示：" |
| 16 | 2100 | fig:sync-button | (同 2096) | (与 #15 同句双引用，见上) |
| 17 | 2138 | fig:modified-files-publish | 2136 | "创建远程仓库 + 推送"：" → "创建远程仓库 + 推送，如图~\ref{fig:modified-files-publish} 所示：" |
| 18 | 2315 | fig:sc-overview-annotated | 2313后 | 在 \subsection{界面详解}（行 2313）后、图（2315）前**插入新句**："界面各区域如图~\ref{fig:sc-overview-annotated} 所示——" |
| 19 | 2341 | fig:sc-graph | 2339 | "点击可查看详细信息：" → "点击可查看详细信息，如图~\ref{fig:sc-graph} 所示：" |
| 20 | 2347 | fig:stage-detail | 2345后 | 在 \subsection{暂存文件}（行 2345）后、图（2347）前**插入新句**："暂存操作如图~\ref{fig:stage-detail} 所示——" |
| 21 | 2353 | fig:stage-lines | 2351 | "可以只暂存其中一部分：" → "可以只暂存其中一部分，如图~\ref{fig:stage-lines} 所示：" |
| 22 | 2371 | fig:sync-changes | 2369 | "可以一键执行 pull + push：" → "可以一键执行 pull + push，如图~\ref{fig:sync-changes} 所示：" |
| 23 | 2379 | fig:create-branch-alt | 2377 | "VS Code 会自动从当前分支创建并立即切换：" → "VS Code 会自动从当前分支创建并立即切换，如图~\ref{fig:create-branch-alt} 所示：" |
| 24 | 2385 | fig:timeline | 2383 | "显示当前文件的所有提交历史：" → "显示当前文件的所有提交历史，如图~\ref{fig:timeline} 所示：" |
| 25 | 2409 | fig:gutter-blame | 2407 | "显示最后修改者和时间：" → "显示最后修改者和时间，如图~\ref{fig:gutter-blame} 所示：" |
| 26 | 2542 | fig:view-changes | 2540 | "在 VS Code 中的操作步骤：" → "在 VS Code 中的操作步骤，如图~\ref{fig:view-changes} 所示：" |

**通用替换规则**（上表未覆盖的边界情况，m3 修复）：
- 单引导句接连续多图（非 minipage，如 2098/2100）→ 引导句列全部图号"如图~\ref{X} 与图~\ref{Y} 所示"
- 图前为节标题无引导句（如 2315、2347）→ 节标题后、图前插入新句"XX 如图~\ref{X} 所示——"
- 代码块后直接图（如 1774）→ 代码块前的引导句末加"（如图~\ref{X} 所示）"

**Assumption 记录（M3）**：假设 `float` 包 `[H]` 在 minipage 内与 `\label` 兼容（常见 LaTeX 模式）。若 T7 编译报 minipage float 错误，**降级方案**：去掉 1351/1355 的 minipage 包装，改用两个独立 `\vscodeimgnarrow` figure 用 `\hfill` 并排（caption 各自独立，label 不变）。

**编译器路径**：`C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe`

## Todos

- [x] 1. 改造图片命令定义、导言区 \figurename 配置、创建 evidence 目录
  - References: `Git_VSCode_Tutorial.tex` 行 86-154
  - 改造：`\newcommand{\vscodeimg}[2]{...}` → `[3]`，`\caption{#2}` 后追加 `\label{#3}`；`\vscodeimgnarrow`、`\vscodeimgwide` 同步。改造后命令体示例：`\newcommand{\vscodeimg}[3]{\begin{figure}[H]\centering\fcolorbox{imgborder}{white}{\includegraphics[width=0.92\textwidth]{#1}}\caption{#2}\label{#3}\end{figure}}`
  - 导言区（行 129 之后、132 之前，tcolorbox 定义结束、图片命令定义之前）插入 `\renewcommand{\figurename}{图}`（M1 确定化：grep 确认当前文档零 `\figurename`，必加，无条件分支）
  - 创建目录 `.omo/evidence/`（m5）
  - Acceptance: grep `\newcommand{\vscodeimg}\[3\]` 等三命令均命中且含 `\label{#3}`；grep `\\renewcommand\{\\figurename\}\{图\}` 命中 1 处；`.omo/evidence/` 目录存在。**本 todo 不编译**（B1：旧调用未更新会报错）
  - QA happy: `grep -c 'newcommand.*\[3\]' Git_VSCode_Tutorial.tex` = 3；`grep -c '\\label{#3}' Git_VSCode_Tutorial.tex` = 3；证据 `.omo/evidence/t1-grep.txt`
  - QA failure: 三命令 `[3]` 命中数<3 → 漏改某命令，回查行 132-154；`\figurename` 无匹配 → 未成功插入，回查行 129-132
  - Commit: `改造图片命令签名 [2]->[3] 加 \label；配置 \figurename；建 evidence 目录`

- [x] 2. 第 6 章 + 第 2 章安装节图片补 label 与 \ref（#1 737、#2 1134、#3 1156）
  - References: 行 732-741、1130-1157；逐处替换表 #1-#3
  - 改造 3 处调用补第 3 参数：`{fig:sc-overview}`、`{fig:init-repo}`、`{fig:clone-repo}`
  - 正文 \ref 替换：行 734"下图展示了 VS Code"→"如图~\ref{fig:sc-overview} 所示，VS Code"；行 1132"按钮："→"按钮，如图~\ref{fig:init-repo} 所示："；行 1154"克隆操作："→"克隆操作，如图~\ref{fig:clone-repo} 所示："
  - Acceptance: 三处调用均含第 3 参数；grep 三 `\ref{fig:}` 命中；原"下图展示了"在 734 行已消失
  - QA happy: grep `{fig:sc-overview}`、`{fig:init-repo}`、`{fig:clone-repo}` 各命中 1 处调用；grep `下图展示了` 在行 734 不再命中；证据 `.omo/evidence/t2-grep.txt`。**不编译**
  - QA failure: 某 `\ref` 缺失 → 引导句替换遗漏，回查替换表 #1-#3
  - Commit: `第 6/2 章图片补 label 与交叉引用`

- [x] 3. 第 3 章基础操作图片补 label 与 \ref（#4 1235、#4b 1237、#5 1273、#6 1296）
  - References: 行 1233-1296；逐处替换表 #4-#6
  - 改造 3 处调用补第 3 参数：`{fig:modified-files}`、`{fig:stage-changes}`、`{fig:commit-button}`
  - 正文 \ref 替换：行 1233"视图里："→"视图里，如图~\ref{fig:modified-files} 所示："；行 1237"上图中"→"图~\ref{fig:modified-files} 中"（同图第二引用）；行 1271"（全部暂存）："→"（全部暂存），如图~\ref{fig:stage-changes} 所示："；行 1294"点击提交按钮。"→"点击提交按钮，如图~\ref{fig:commit-button} 所示。"
  - Acceptance: 三处第 3 参数齐；四处 \ref（含 1237 二引用）；grep 原"上图中"在 1237 不再命中
  - QA happy: grep `{fig:modified-files}`、`{fig:stage-changes}`、`{fig:commit-button}` 各命中；`grep '图~\\ref{fig:modified-files} 中'` 命中 1237；证据 `.omo/evidence/t3-grep.txt`。**不编译**
  - QA failure: 1237"上图中"仍命中 → 二引用遗漏
  - Commit: `第 3 章基础操作图片补 label 与交叉引用（含同图二引用）`

- [x] 4. diff 相关图片补 label 与 \ref（#7 1335、#8/#9 1351/1355 minipage、#10 1363）
  - References: 行 1333-1363；逐处替换表 #7-#10
  - 改造 4 处调用补第 3 参数：`{fig:diff-editor}`、`{fig:diff-sidebyside}`、`{fig:diff-inline}`、`{fig:gutter-diff}`
  - minipage 并排（1351/1355）：两个 `\vscodeimgnarrow` 各补第 3 参数，两 label 独立
  - 正文 \ref 替换：行 1333"打开差异对比视图："→"...视图，如图~\ref{fig:diff-editor} 所示："；行 1346"对比如下——"→"对比如图~\ref{fig:diff-sidebyside} 与图~\ref{fig:diff-inline} 所示——"；行 1361"显示变更指示："→"显示变更指示，如图~\ref{fig:gutter-diff} 所示："
  - Acceptance: 四处第 3 参数齐；minipage 内两图各独立 label；行 1346 含两个 \ref
  - QA happy: grep `{fig:diff-sidebyside}` 与 `{fig:diff-inline}` 在 minipage 区命中；`grep '图~\\ref{fig:diff-sidebyside} 与图~\\ref{fig:diff-inline}'` 命中 1346；证据 `.omo/evidence/t4-grep.txt`。**不编译**
  - QA failure: minipage 两图 label 混淆 → 回查 1351/1355 第 3 参数顺序
  - Commit: `diff 图片补 label 与交叉引用（含 minipage 并排双引用）`
  - Assumption M3：若后续 T7 编译报 minipage float 错误，启用 Execution strategy 的降级方案

- [x] 5. 第 4 章分支与合并图片补 label 与 \ref（#11 1691、#12 1695、#13 1774、#14 1789）
  - References: 行 1689-1789；逐处替换表 #11-#14
  - 改造 4 处调用补第 3 参数：`{fig:current-branch}`、`{fig:create-branch}`、`{fig:merge-conflict}`、`{fig:merge-editor}`
  - 正文 \ref 替换：行 1689"切换或创建新分支："→"...，如图~\ref{fig:current-branch} 所示："；行 1693"创建并切换："→"...，如图~\ref{fig:create-branch} 所示："；行 1764"插入冲突标记："→"插入冲突标记（如图~\ref{fig:merge-conflict} 所示）："；行 1787"三方合并编辑器："→"...，如图~\ref{fig:merge-editor} 所示："
  - Acceptance: 四处第 3 参数齐；四处 \ref
  - QA happy: grep 四 label 键各命中调用 1 处；证据 `.omo/evidence/t5-grep.txt`。**不编译**
  - QA failure: 1774 引导句替换遗漏（代码块后图边界情况）→ 回查替换表 #13
  - Commit: `第 4 章分支合并图片补 label 与交叉引用`

- [x] 6. 第 5 章远程仓库图片补 label 与 \ref（#15 2098、#16 2100、#17 2138，含连续双图与复用图）
  - References: 行 2096-2138；逐处替换表 #15-#17
  - 改造 3 处调用补第 3 参数：`{fig:pull-push}`、`{fig:sync-button}`、`{fig:modified-files-publish}`（13-modified-files.png 第 2 次复用，与 #4 `fig:modified-files` 区分）
  - 正文 \ref 替换：行 2096"同步按钮一键完成："→"...，如图~\ref{fig:pull-push} 与图~\ref{fig:sync-button} 所示："（m3 双图规则，单引导句引两图）；行 2136"创建远程仓库 + 推送"："→"...，如图~\ref{fig:modified-files-publish} 所示："
  - Acceptance: 三处第 3 参数齐；复用图 label `fig:modified-files-publish` 唯一不与 `fig:modified-files` 冲突；行 2096 含两 \ref
  - QA happy: grep `fig:modified-files-publish` 仅命中 1 处 label；`grep '图~\\ref{fig:pull-push} 与图~\\ref{fig:sync-button}'` 命中 2096；证据 `.omo/evidence/t6-grep.txt`。**不编译**
  - QA failure: 若 `fig:modified-files` 与 `fig:modified-files-publish` 混淆 → 回查 1235 vs 2138 第 3 参数
  - Commit: `第 5 章远程仓库图片补 label 与交叉引用（含连续双图与复用图）`

- [x] 7. 第 9 章速查卡补 label 与 \ref（#18 2315、#19 2341、#20 2347、#21 2353、#22 2371、#23 2379、#24 2385、#25 2409、#26 2542）+ 首次编译验证
  - References: 行 2313-2542；逐处替换表 #18-#26
  - 改造 9 处调用补第 3 参数：`{fig:sc-overview-annotated}`（01-sc-overview.png 第 2 次复用，与 #1 `fig:sc-overview` 区分）、`{fig:sc-graph}`、`{fig:stage-detail}`、`{fig:stage-lines}`、`{fig:sync-changes}`、`{fig:create-branch-alt}`（19-create-branch.png 第 2 次复用，与 #12 `fig:create-branch` 区分）、`{fig:timeline}`、`{fig:gutter-blame}`、`{fig:view-changes}`
  - 正文 \ref 替换（含两处节标题后插新句）：行 2313 \subsection{界面详解} 后、2315 图前**插入新句**"界面各区域如图~\ref{fig:sc-overview-annotated} 所示——"；行 2345 \subsection{暂存文件} 后、2347 图前**插入新句**"暂存操作如图~\ref{fig:stage-detail} 所示——"；其余 #19/#21/#22/#23/#24/#25/#26 按替换表
  - **首次编译**：全部 26 处更新完成后，运行 `C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex` 两次
  - Acceptance: 九处第 3 参数齐；两复用图 label 唯一；xelatex 两次无致命错误；编译日志无 `Reference undefined`、无 `multiply defined`
  - QA happy: grep `fig:sc-overview-annotated` 与 `fig:create-branch-alt` 各仅命中 1 处 label；grep `\label{fig:` 全文计数=26；编译日志无 undefined/multiply；证据 `.omo/evidence/t7-grep.txt` + `.omo/evidence/t7-xelatex-log-1.txt` + `.omo/evidence/t7-xelatex-log-2.txt`
  - QA failure: `Reference undefined` → label/\ref 拼写校对（回查替换表）；`multiply defined` → 复用图后缀漏加；minipage float 错误 → 启用 M3 降级方案后重编译
  - Commit: `第 9 章速查卡补 label 与交叉引用（含复用图独立 label）+ 首次编译验证`

## Final verification wave

- [x] F1. 计划合规审计
  - 校验：grep `\label{fig:` 计数=26；grep `\newcommand{\vscodeimg` 等三命令均 `[3]` 且含 `\label{#3}`；grep `\renewcommand{\figurename}{图}` 命中；三处复用图 label 各唯一（`fig:sc-overview`/`fig:sc-overview-annotated`、`fig:modified-files`/`fig:modified-files-publish`、`fig:create-branch`/`fig:create-branch-alt`）
  - 证据：`.omo/evidence/f1-compliance-grep.txt`
- [x] F2. LaTeX 编译与引用一致性审查（m2 修复，agent-executable）
  - xelatex 两次最终日志无致命错误、无 `Reference undefined`、无 `multiply defined`；用 `look_at` 工具读取 `Git_VSCode_Tutorial.pdf` 抽查 3 页，确认图标题渲染为"图 N"且正文 `\ref` 渲染为正确编号
  - 证据：`.omo/evidence/f2-xelatex-final-log.txt` + `.omo/evidence/f2-pdf-lookat.txt`
- [x] F3. 实际 QA（PDF 翻阅）
  - 用 `look_at` 工具逐章翻阅 PDF，确认 26 张图均有"图 N：xxx"标题；正文每处"如图~\ref{} 所示"渲染为正确图号；3 处复用图编号各不相同；minipage 并排两图各有独立编号
  - 证据：`.omo/evidence/f3-pdf-qa-checklist.md`
- [x] F4. 范围保真审计
  - git diff 确认未改任何 caption 文字（diff 应仅触及行 86-154 命令定义区 + 26 处调用的第 3 参数 + 26 处引导句 + \figurename 一行 + 2315/2347 两处新插句）；未加 `\listoffigures`；未改 tcolorbox 环境；未改 images/；未引新包；未改 sc-*.md
  - 证据：`.omo/evidence/f4-scope-fidelity-git-diff.txt`

## Commit strategy

原子提交，每个 todo 一个 commit，中文消息（项目约定：中文正文 + 英文标识符）。所有 commit 在主分支或任务 worktree（由 worker 决定，`/start-work --worktree`）。F1-F4 不产生 commit（验证性质）。T2-T6 commit 时**不编译**，仅 grep 校验后提交；T7 commit 含首次编译验证结果。

## Success criteria

- 26 处图片调用均有唯一 `\label{fig:xxx}`，键名与逐处替换表一一对应
- 正文每处引导句含 `\ref{fig:xxx}` 交叉引用，PDF 渲染为"图 N"
- 3 处复用图（01-sc-overview、13-modified-files、19-create-branch）各有独立编号，无 multiply defined 警告
- minipage 并排两图（1351/1355）各有独立 label
- xelatex 两次编译无致命错误、无 undefined reference、无 multiply defined
- caption 文字内容零改动；tcolorbox 环境零改动；images/ 零改动；零新依赖
