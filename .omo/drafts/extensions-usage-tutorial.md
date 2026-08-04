# Draft: extensions-usage-tutorial

## Metadata

| Field | Value |
|-------|-------|
| slug | extensions-usage-tutorial |
| intent | clear |
| review_required | false |
| status | approved |
| created | 2026-08-03 |

## Intent

用户要求为文档第 6 章 `\section{推荐扩展}`（行 2426-2453）补充 5 个推荐 VS Code 扩展的完整使用方法教程。

## Decisions

### Scope

- **IN**: 针对表格中列出的 5 个扩展（GitLens、GitHub Pull Requests、GitHub Issues、Git Graph、Git History），各新增一个 `\subsection` 详细教程
- **OUT**: 不新增章节、不新增 tcolorbox 环境、不修改现有表格和 GitLens 提示段落

### Approach

- 每个扩展：简介 → 安装 → 核心用法（3-5 条，含命令面板入口） → 实用技巧
- 截图来源：从 VS Code Marketplace / GitHub README 抓取官方截图，不以现有文档风格为约束
- 篇幅：预估新增 250-350 行 LaTeX

### Screenshot strategy

- 不要求与现有文档截图风格一致
- 优先从官方 Marketplace 页面或 GitHub README 获取
- 图片命名：28-description.png, 29-description.png... 放入 images/
- 不可获取时使用文字描述代替，预留 `\vscodeimg` + label 占位

### Format

- 中文正文，技术标识符/命令用英文
- 遵循现有 prose + bullet list 风格
- 不新增 tcolorbox 环境

## Approval

- 审批简报已呈递，用户确认方案并澄清截图不需风格一致
- 状态：已批准 → 生成执行计划

## Components

| ID | Component | Outcome | Status |
|----|-----------|---------|--------|
| C1 | 获取扩展截图 | 从 web 下载官方截图到 images/ | pending |
| C2 | 撰写扩展教程内容 | 5 个 \subsection 的 LaTeX 源码 | pending |
| C3 | 集成到 .tex 文件 | 在行 2453 后插入新内容 | pending |
| C4 | 编译验证 | xelatex 两次无致命错误 | pending |
