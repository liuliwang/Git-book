# AGENTS.md — Git + VS Code 教程文档项目

## 项目概览

本项目是面向初学者的 Git + VS Code 教程文档，产出为 58 页 LaTeX PDF（Git_VSCode_Tutorial.pdf），源文件为 Git_VSCode_Tutorial.tex。

## 构建与编译

- 编译首行固定为：`% !TEX program = xelatex`
- 完整编译命令：
  `C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex`
- 需连续编译两次：首次生成 .aux/.toc，第二次正确输出目录与交叉引用

## 项目结构

| 路径 | 说明 |
|------|------|
| `Git_VSCode_Tutorial.tex` | 主文档（LaTeX 源文件，唯一可编辑的文档源） |
| `images/` | 截图目录（27 张，命名 `NN-description.png`） |
| `sc-*.md` | 参考文档（sc-overview / sc-quickstart / sc-staging / sc-branches，共 4 份，只读） |
| `.omo/` | 计划与证据（plans/、evidence/、drafts/、run-continuation/） |

## 文档撰写基本要求

本专题为撰写教程文档的硬性要求。每条均为「规则 + 正反例」，撰写与修改时必须逐条对照。

### 受众与语言

- 规则：中文正文；Git 术语首次出现附英文原文（如「暂存区（stage）」）；面向初学者，由浅入深
- 正面：`暂存区（stage）是待提交更改的存放位置`
- 反面：首现术语不附英文；堆砌专业名词不做解释

### 章节结构

- 规则：固定 7 章 + 附录（第 8 部分为「附录：常用命令速查表」）；操作用 `stepbox`、概念用 `keybox`、警示用 `warnbox`
- 正面：新增操作步骤写入 `stepbox`，核心概念写入 `keybox`
- 反面：把警告内容放进 `stepbox`；在既有章节之外擅增章节

### 图文并茂

- 规则：关键操作必须配图；只引用 `images/` 中真实存在的文件；用 `\vscodeimg` / `\vscodeimgnarrow` / `\vscodeimgwide` 嵌入且必须带 caption
- 正面：`\vscodeimg{images/14-stage-button.png}{点击暂存更改按钮}`，且文件确实存在于 `images/`
- 反面：引用不存在的 `images/foo.png`；嵌入图片却省略 caption

### 命令准确性

- 规则：所有命令真实可执行、复制即用；shell 代码用 listings `shell` 样式
- 正面：`git add .` 放入 `shell` 样式代码块，可直接复制执行
- 反面：命令拼写错误；用普通文本混排命令

### 术语一致性

- 规则：全文统一译法，不混用中英表达
- 正面：全文统一使用「暂存区（stage）」
- 反面：一章写「暂存区」，另一章写「staging area」

### 质量门槛

- 规则：修改后必须 xelatex 编译两次通过、无致命错误；新截图按 `NN-description.png` 命名并放入 `images/`
- 正面：新增截图 `27-rebase.png` 放入 `images/`，编译通过后交付
- 反面：编译存在致命错误仍交付；截图随意命名（如 `fig1.png`）

### 安全边界

- 规则：绝不出现真实密钥/令牌；绝不伪造截图
- 正面：示例令牌使用占位符（如 `xxx`）
- 反面：粘贴真实 token；用图像处理合成假截图冒充真实界面

## LaTeX 技术约定

### tcolorbox 环境（5 个）

| 环境 | 颜色 | 默认标题 | 用途 |
|------|------|---------|------|
| `tipbox` | 绿色 | 提示 | 提示 |
| `warnbox` | 红色 | 注意 | 警示 |
| `infobox` | 蓝色 | 说明 | 说明 |
| `keybox` | 橙色 | 核心概念 | 核心概念 |
| `stepbox` | 紫色 | 操作步骤 | 操作步骤 |

### 图片命令（3 个）

| 命令 | 宽度 | 用途 |
|------|------|------|
| `\vscodeimg` | `0.92\textwidth` | 默认宽度 |
| `\vscodeimgnarrow` | `0.72\textwidth` | 窄幅 |
| `\vscodeimgwide` | `\textwidth` | 全宽 |

### listings 与宏包

- listings 代码样式：`shell`
- 宏包共 19 个，主要如下：geometry、graphicx、xcolor、listings、booktabs、longtable、array、enumitem、tcolorbox、fontawesome5、hyperref、tikz、fancyhdr、titlesec、tabularx、multicol、float、caption、wrapfig

## 参考文档

- 四份参考文档：`sc-overview.md`、`sc-quickstart.md`、`sc-staging.md`、`sc-branches.md`
- 说明：只读，作为章节内容的权威事实来源，不得修改

## 安全边界

### Always（总是执行）

- 修改后必须 xelatex 编译两次验证通过再交付
- 新截图放入 `images/` 并按 `NN-description.png` 命名

### Ask first（需确认）

- 删除图片
- 修改章节结构

### Never（永不执行）

- 修改 `sc-*.md` 参考文档
- 提交含 secrets 的文件
- 伪造截图

## 备注

- 篇幅目标 ≤150 行，硬上限 300 行
- 正文用中文，技术标识符/命令用英文
- 本文件为活文档；项目约定变化时同步更新
