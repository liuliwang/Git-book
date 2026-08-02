# AGENTS.md — Git + VS Code 教程文档项目

## 项目概览

面向初学者的 Git + VS Code 教程文档。唯一产出是 `Git_VSCode_Tutorial.pdf`（LaTeX），源文件为 `Git_VSCode_Tutorial.tex`。

## 构建命令

```powershell
# 编译（必须连续执行两次：首次生成 .aux/.toc，第二次输出正确目录与交叉引用）
C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex
C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex
```

- 首行固定为：`% !TEX program = xelatex`
- 编译无致命错误即视为通过（允许 Overfull/Underfull 警告）
- 首次编译日志中 "fatal: refusing to" 是文档正文中的 Git 报错示例文本，非编译错误
- **必须编译两次**：第一次生成 `.aux`/`.toc`，第二次才能正确解析交叉引用（`\ref`/`\label`）

## 项目结构

| 路径 | 说明 |
|------|------|
| `Git_VSCode_Tutorial.tex` | 唯一可编辑的文档源（~3100 行） |
| `images/` | 截图目录（27 张），命名 `NN-description.png` |
| `sc-*.md` | 参考文档（sc-overview / sc-quickstart / sc-staging / sc-branches），**只读** |
| `.omo/` | 计划与证据（plans/、evidence/、drafts/、notepads/） |

## 文档撰写规范

**语言**：中文正文；Git 术语首次出现附英文原文（如「暂存区（stage）」）。

### tcolorbox 环境（5 个，定义于行 86-129）

| 环境 | 视觉样式 | 图标 | 标签 | 用途 |
|------|---------|------|------|------|
| `tipbox` | 无框，粗体标题 | `\faLightbulb`（黄色） | 提示 | 提示/总结 |
| `warnbox` | 白底 + 纯红细框（`warnred`） | `\faExclamationTriangle`（红色） | 注意 | 警示 |
| `infobox` | 白底 + 纯蓝细框（`infoblue`） | `\faInfoCircle`（蓝色） | 说明 | 补充说明 |
| `keybox` | 无框，粗体标题 | `\faKey`（橙色） | 核心概念 | 关键理解 |
| `stepbox` | 无框，粗体标题 | `\faCogs`（紫色） | 操作步骤 | 操作步骤 |

- **tipbox / keybox / stepbox 是 `\newenvironment`**，正文中 `[...]` 可选参数会导致编译错误
- **warnbox / infobox 是 `\newtcolorbox`**，保留细框，各自有专属颜色
- 不得新增环境类型；不得修改图标与标签文本

### 颜色定义

| 颜色 | 值 | 用途 |
|------|-----|------|
| `warnred` | RGB(198,40,40) | warnbox 边框 + 警示文字 |
| `infoblue` | RGB(21,101,192) | infobox 边框 + 说明文字 |
| `tipgreen` | RGB(46,125,50) | tipbox 标题 |
| `gitorange` | RGB(240,80,50) | 文档主题色 |
| `codebg` / `codeframe` | RGB(248,248,248) / (200,200,200) | 代码块 |

### 图片命令

- `\vscodeimg{路径}{caption}{label}` — 默认宽度 `0.92\textwidth`，**3 参数**（第 3 参数为 `\label` 键）
- `\vscodeimgnarrow{路径}{caption}{label}` — 窄幅 `0.72\textwidth`
- `\vscodeimgwide{路径}{caption}{label}` — 全宽 `\textwidth`

- 所有图片必须有 caption；只引用 `images/` 中真实存在的文件
- 新截图按 `NN-description.png` 命名并放入 `images/`
- Shell 代码用 `listings` 的 `shell` 样式
- **图编号格式**：`\renewcommand{\figurename}{图}`（行 130），caption 渲染为「图 N.N：名称——说明」

### 图片引用体系（37 图）

文档现有 **37 个 figure**（26 张位图 + 11 个 TikZ 示意图），全部配有 `\caption` + `\label` + 正文 `\ref`：

- **26 张位图**：通过 `\vscodeimg`/`\vscodeimgnarrow`/`\vscodeimgwide` 插入（`images/` 目录），已完成交叉引用
- **11 个 TikZ 示意图**：原为 `\begin{center}...\end{center}` 内联图，已转为 `\begin{figure}[H]\centering...\caption{...}\label{...}\end{figure}`，含 caption 和 label
- **TikZ 图 wrapper 模式**：
  - 非 resizebox 图：`\begin{center}` → `\begin{figure}[H]\centering`，`\end{center}` → `\caption{...}\label{...}\end{figure}`
  - resizebox 图（5 个）：caption/label 放在 `\resizebox` 的 `}` 之后、`\end{figure}` 之前
- 所有 `\ref{fig:xxx}` 在两次编译后自动解析为正确图号

### 章节

文档共 10 章（第 0-9 章）+ 附录：

0. 开始前的准备——建立心智模型
1. 版本控制与 Git 概述
2. Git 安装与配置
3. Git 基础操作
4. 分支与合并
5. 远程仓库与 GitHub
6. VS Code 中的 Git 集成
7. 实战工作流
8. 新手避坑指南
9. 可视化速查卡

附录：常用命令速查表

- 不得在既有章节外擅增章节
- 第 1 章已包含 BitKeeper 历史、分布式/集中式对比、Tag 对象、HEAD 与引用等深度内容

## 安全边界

- **Always**：修改 `.tex` 后必须 xelatex 编译两次通过再交付
- **Ask first**：删除图片、修改章节结构、修改 `images/` 目录中文件
- **Never**：修改 `sc-*.md` 参考文档、提交含 secrets 的文件、伪造截图、**自动提交 git**

## 参考文档

四份 `sc-*.md` 是章节内容的权威事实来源，**只读**。需要事实依据时优先查阅，不得修改。

## 备注

- 篇幅目标 ≤200 行，硬上限 300 行
- 正文用中文，技术标识符/命令用英文
- 本文件为活文档；项目约定变化时同步更新
