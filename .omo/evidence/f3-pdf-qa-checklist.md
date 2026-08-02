# F3 实际 QA（PDF 翻阅）— tikz-cross-refs

## 结论

**PASS** — 11 个 TikZ 示意图全部有「图 N / 图 C.N」caption，正文 `\ref` 渲染编号与 caption 编号逐一对应；抽查的现有位图引用在编号重排后仍正确。

## 验证方法

1. `pypdf` 全文本提取 86 页，正则定位 11 个 caption（`图 N.N: ...`）与正文引用（`如图 N.N 所示`）
2. `look_at` 视觉抽查 6 页（第 8、9、14、51、81、83 页），确认 TikZ 图存在、caption 文本与编号、正文引用编号
3. 位图抽查 3 处（`sc-overview` / `merge-conflict` / `pull-push`）

## 11 个 TikZ 图 — caption 与 ref 一致性

编号规则：figure 计数器随章重置（report 类默认），第 0 章为「图 N」，第 1 章起为「图 C.N」。

| # | label | caption 编号 | caption 页码 | ref 页码 | ref 文本（PDF 渲染） | 一致 |
|---|-------|-------------|-------------|---------|---------------------|------|
| 1 | `fig:three-analogies` | 图 1 | 8 | 7 | 三者关系如图 1 所示 | ✅ |
| 2 | `fig:learning-path` | 图 2 | 9 | 9 | 完整路径如图 2 所示 | ✅ |
| 3 | `fig:git-objects` | 图 1.1 | 14 | 14 | 如图 1.1 所示 | ✅ |
| 4 | `fig:commit-structure` | 图 1.2 | 16 | 15 | 如图 1.2 所示 | ✅ |
| 5 | `fig:three-zones` | 图 1.3 | 18 | 18 | 如图 1.3 所示 | ✅ |
| 6 | `fig:file-states` | 图 3.3 | 32 | 31 | 如图 3.3 所示 | ✅ |
| 7 | `fig:merge-rebase` | 图 4.5 | 51 | 51 | 如图 4.5 所示 | ✅ |
| 8 | `fig:pr-workflow` | 图 5.4 | 61 | 61 | 如图 5.4 所示 | ✅ |
| 9 | `fig:cheatsheet-zones` | 图 8.1 | 81 | 81 | 如图 8.1 所示 | ✅ |
| 10 | `fig:cheatsheet-merge-rebase` | 图 8.2 | 82 | 82 | 如图 8.2 所示 | ✅ |
| 11 | `fig:cheatsheet-remote` | 图 8.3 | 83 | 83 | 如图 8.3 所示 | ✅ |

**11/11 一致。**

## look_at 视觉抽查（6 页）

| 页码 | TikZ 图 | caption（视觉确认） | 正文 ref |
|------|---------|---------------------|---------|
| 8 | ✅ | 图 1: Git、GitHub 与 VS Code 的关系——三者协作构成完整的版本控制工作流 | — |
| 9 | ✅ | 图 2: 本教程学习路径——从心智模型到实战工作流的章节安排 | 如图 2 所示 |
| 14 | ✅ | 图 1.1: Git 的四个核心对象——Blob、Tree、Commit、Branch 的层次关系 | 如图 1.1 所示 |
| 51 | ✅ | 图 4.5: Merge 与 Rebase 的对比——Merge 保留完整历史产生合并节点, Rebase 生成线性历史 | 如图 4.5 所示 |
| 81 | ✅ | 图 8.1: Git 四大区域数据流转速查——工作区、暂存区、本地仓库、远程仓库之间的完整操作路径 | 如图 8.1 所示 |
| 83 | ✅ | 图 8.3: 远程协作完整链路速查——从本地开发到 GitHub 合并的完整流程 | 如图 8.3 所示 |

## 位图抽查（新增图重排后仍正确引用）

| label | 编号 | ref 页码 | caption 页码 |
|-------|------|---------|-------------|
| `fig:sc-overview` | 图 1.4 | 19（如图 1.4 所示） | 19 |
| `fig:merge-conflict` | 图 4.3 | 48（如图 4.3 所示） | 49（[H] 落页） |
| `fig:pull-push` | 图 5.1 | 57（如图 5.1 与图 5.2 所示） | 57 |

**3/3 抽查通过。** 位图引用经 `\ref` 两次编译自动解析，编号重排后无错链。

## 判定依据

- 文本层：11/11 caption 编号 == ref 渲染编号（含跨章前缀 C.N）
- 视觉层：6 页 look_at 全部确认 caption 文本、编号、引用一致
- 编译层：无 `Reference undefined` / `multiply defined`（见 F2 日志证据）
- 抽查位图引用无错链

## 备注

- caption 编号不是连续 1-11 而是「图 C.N」按章编号，属 report 类默认行为，与 plan 中「图 N」措辞的意图一致（渲染为正确图号即可）
- `fig:commit-structure`（图 1.2）ref 在 p15、caption 在 p16，为 [H] 浮动正常落页，编号一致
