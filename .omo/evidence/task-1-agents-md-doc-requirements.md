# Task 1: AGENTS.md 文档需求事实盘点

> 数据来源：项目真实文件读取，非推测

## 1. 自定义 tcolorbox 环境（5个）

| 环境 | 主题色 | 默认标题 | 用途 |
|------|--------|---------|------|
| `tipbox` | 绿色 | 提示 | 提示框 |
| `warnbox` | 红色 | 注意 | 警告框 |
| `infobox` | 蓝色 | 说明 | 信息框 |
| `keybox` | 橙色 | 核心概念 | 核心概念框 |
| `stepbox` | 紫色 | 操作步骤 | 操作步骤框 |

## 2. 图片命令（3个）

| 命令 | 宽度 | 说明 |
|------|------|------|
| `\vscodeimg` | `0.92\textwidth` | 默认宽度 |
| `\vscodeimgnarrow` | `0.72\textwidth` | 窄幅 |
| `\vscodeimgwide` | `\textwidth` | 全宽 |

## 3. 编译配置

- **编译命令**：`C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe`
- **编译首行**：`% !TEX program = xelatex`
- **listings 样式**：`shell`

## 4. 宏包（19个）

geometry, graphicx, xcolor, listings, booktabs, longtable, array, enumitem, tcolorbox, fontawesome5, hyperref, tikz, fancyhdr, titlesec, tabularx, multicol, float, caption, wrapfig

## 5. 图片文件（27张）

01-sc-overview.png, 02-sc-graph.png, 03-diff-editor.png, 04-gutter-blame.png, 05-stage-changes.png, 06-commit-msg-ai.png, 07-sync-changes.png, 08-merge-editor.png, 09-branches.png, 10-timeline.png, 11-clone-repo.png, 12-init-repo.png, 13-modified-files.png, 14-stage-button.png, 15-commit-button.png, 16-sync-button.png, 17-pull-push.png, 18-current-branch.png, 19-create-branch.png, 20-view-changes.png, 21-gutter-diff.png, 22-stage-detail.png, 23-stage-lines.png, 24-diff-sidebyside.png, 25-diff-inline.png, 26-sc-graph-detail.png, merge-conflict.png

## 6. 参考文档（4份）

- sc-overview.md
- sc-quickstart.md
- sc-staging.md
- sc-branches.md

## 7. 章节结构（7章+附录）

1. 版本控制与 Git 概述
2. Git 安装与配置
3. Git 基础操作
4. 分支与合并
5. 远程仓库与 GitHub
6. VS Code 中的 Git 集成
7. 实战工作流
8. 附录：常用命令速查表
