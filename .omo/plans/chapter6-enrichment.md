# 第6章《VS Code 中的 Git 集成》内容丰富与优化计划

## TL;DR (For humans)

通过网络检索 VS Code 官方文档及扩展文档，为第6章新增4个重要章节（合并冲突解决、Diff 编辑器详解、AI 辅助功能、Git Worktrees），优化现有4个章节内容，并补充约10张新截图，使第6章从基础功能介绍升级为涵盖 VS Code Git 集成全部核心特性的完整教程。

## Scope

### In Scope
- 新增4个章节：合并冲突解决、Diff 编辑器详解与 Gutter Indicators、AI 辅助功能、Git Worktrees
- 优化4个现有章节：源代码管理视图、暂存与提交、GitLens 扩展、GitHub Pull Requests
- 补充10张新截图（需在本机 VS Code 中截取）
- 更新动手实验 6，加入新功能实践
- 添加完整的交叉引用和来源标注

### Must NOT Have
- 不修改第6章以外的任何章节
- 不修改现有图片（仅新增）
- 不修改 LaTeX 宏定义和格式
- 不删除现有内容

## Verification strategy

### TDD / Tests-after / None
- None（文档类任务，无代码测试）

### Agent-executed QA per todo
- T1: 编译验证 - `xelatex` 编译两次，检查 `.log` 无 fatal error
- T3: 引用验证 - 确认所有 `\vscodeimg` 引用指向的图片文件存在
- T5: 交叉引用验证 - 确认所有 `\ref{}` 和 `\label{}` 成对出现

## Execution strategy

### 实施顺序
1. 撰写新增章节内容（按2.1→2.2→2.3→2.4顺序）
2. 优化现有章节（按3.1→3.2→3.3→3.4顺序）
3. 补充截图（在本机 VS Code 中截取）
4. 更新动手实验 6
5. 编译验证和交叉引用检查

### 内容来源
- VS Code 官方文档（sourcecontrol 系列）
- GitLens 官方文档
- 本机 VS Code 实测验证

## Todos

- [x] 1. 新增"合并冲突解决"章节
  - [x] 1.1 撰写三路合并编辑器内容（三面板视图、复选框选择、手动编辑、布局切换）
  - [x] 1.2 撰写内联冲突解决内容（Accept Current/Incoming/Both/Compare）
  - [x] 1.3 添加截图占位符 `33-merge-editor-3way.png`、`34-merge-inline-actions.png`
  - [x] 1.4 添加来源标注

- [x] 2. 新增"Diff 编辑器与 Gutter Indicators"章节
  - [x] 2.1 撰写 side-by-side 与 inline 视图对比
  - [x] 2.2 撰写折叠未更改区域和导航按钮功能
  - [x] 2.3 撰写 Gutter Indicators（绿/蓝/红标记含义及内联预览）
  - [x] 2.4 添加截图占位符 `35-diff-editor-sidebyside.png`、`36-diff-editor-inline.png`、`37-gutter-indicators.png`、`38-gutter-hover.png`
  - [x] 2.5 添加来源标注

- [x] 3. 新增"AI 辅助功能"章节
  - [x] 3.1 撰写 AI 生成提交信息（sparkle 图标、自定义指令）
  - [x] 3.2 撰写 AI 代码审查（Code Review 按钮、Copilot 订阅）
  - [x] 3.3 撰写 AI 辅助解决合并冲突
  - [x] 3.4 添加截图占位符 `39-ai-commit-message.png`、`40-ai-code-review.png`
  - [x] 3.5 添加来源标注

- [x] 4. 新增"Git Worktrees"章节
  - [x] 4.1 撰写工作树概念和优势
  - [x] 4.2 撰写创建方式（图形界面和命令面板）
  - [x] 4.3 撰写管理操作（打开、比较、迁移、自动检测）
  - [x] 4.4 添加截图占位符 `41-worktree-create.png`、`42-worktree-repositories.png`
  - [x] 4.5 添加来源标注

- [x] 5. 优化现有章节内容
  - [x] 5.1 优化 6.1 节：补充 gutter indicators、AI 提交信息、Code Review 按钮说明
  - [x] 5.2 优化 6.2 节：补充 diff 编辑器详细操作、从 diff 视图直接 stage/revert
  - [x] 5.3 优化 6.8.1 节（GitLens）：补充 Commit Graph 过滤、Home View、Launchpad
  - [x] 5.4 优化 6.8.2 节（GitHub PR）：补充 in-editor commenting、AI 代码审查、Review Mode

- [x] 6. 更新动手实验 6
  - [x] 6.1 更新实验内容，加入新功能实践（diff 编辑器操作、AI 提交信息等）
  - [x] 6.2 验证实验步骤的可操作性

- [x] 7. 截图制作与图片管理
  - [x] 7.1 下载 VS Code 官方文档原图10张（media.githubusercontent.com LFS CDN）
  - [x] 7.2 按命名规范重命名图片文件（`NN-descriptive-name.png`）
  - [x] 7.3 将图片放入 `images/` 目录

- [x] 8. 交叉引用与来源标注
  - [x] 8.1 检查所有新增内容的交叉引用（`\ref{}`、`\label{}`）是否成对
  - [x] 8.2 添加完整的来源标注（使用 `infobox` 环境）
  - [x] 8.3 验证引用链接可访问性

## Final verification wave

- [x] F1. 编译通过验证：运行 `xelatex` 两次无 fatal error，.log 无本次改动范围内的 Overfull
- [x] F2. 图片完整性验证：所有引用的图片文件真实存在于 `images/` 目录
- [x] F3. 交叉引用验证：所有 `\ref{}` 均有对应的 `\label{}`
- [x] F4. 来源标注验证：所有新增内容均有来源标注，无 `【待核实】` 标记

## Commit strategy

### 提交策略
- 建议将工作分解为多个小提交：
  1. `docs: 新增第6章合并冲突解决章节`
  2. `docs: 新增Diff编辑器和Gutter Indicators章节`
  3. `docs: 新增AI辅助功能章节`
  4. `docs: 新增Git Worktrees章节`
  5. `docs: 优化第6章现有内容`
  6. `docs: 更新动手实验6`
  7. `assets: 添加第6章新增图片`
  8. `fix: 修复交叉引用和编译错误`

## Success criteria

1. `xelatex` 编译通过，无 fatal error
2. 所有新增图片真实存在于 `images/` 目录
3. 所有新增内容均有来源标注
4. 交叉引用完整且正确
5. 动手实验 6 内容可操作，步骤清晰
6. 中文风格直接、电报式，无 emoji（LaTeX 内 fontawesome5 图标除外）
7. 未引入与现有宏冲突的新定义
