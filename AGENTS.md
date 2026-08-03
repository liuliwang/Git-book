# AGENTS.md —— Git、GitHub 及 Git 在 VS Code 中的应用 写作规范

## 1. 项目概览与定位

本书为技术专著《Git 与 VS Code 实战教程》，使用 LaTeX（`ctexbook` + `xelatex`）编译，目标读者为零基础到初阶开发者。核心质量支柱：详尽、通俗、图文并茂、可溯源。所有内容必须基于本机实测或权威文档，禁止凭记忆编造。

## 2. 项目结构与文件约定

- **主文件**：`Git_VSCode_Tutorial.tex`（`% !TEX program = xelatex`）
- **图片目录**：`images/` 存放 32 张真实截图，命名格式 `NN-descriptive-name.png`（如 `01-sc-overview.png`、`merge-conflict.png`）。新增截图必须遵循此数字前缀规则。
- **素材文件**：`sc-overview.md`、`sc-quickstart.md`、`sc-staging.md`、`sc-branches.md` 为 VS Code 官方文档英文原文，仅作翻译/改编参考，不可直接作为最终正文。
- **编译产物**：`.aux`、`.log`、`.out`、`.toc`、`.pdf` 为生成文件，禁止手工编辑。

## 3. 编译工具链

编译命令（需执行两次以生成目录）：
```bash
xelatex -interaction=nonstopmode Git_VSCode_Tutorial.tex
xelatex -interaction=nonstopmode Git_VSCode_Tutorial.tex
```
每次修改后必须检查 `.log`，确保无 fatal error 或 missing reference 警告。

## 4. LaTeX 写作规范（复用已有宏，禁止重复定义）

**图片命令**：
- `\vscodeimg{images/XX.png}{caption}{label}` —— 标准宽度（0.92\textwidth）
- `\vscodeimgnarrow{...}{...}{...}` —— 窄版（0.72\textwidth）
- `\vscodeimgwide{...}{...}{...}` —— 宽版（\textwidth）

**提示框环境**：
- `tipbox` —— 提示（\faLightbulb）
- `warnbox` —— 注意（\faExclamationTriangle）
- `infobox` —— 说明（\faInfoCircle）
- `keybox` —— 核心概念（\faKey）
- `stepbox` —— 操作步骤（\faCogs）

**代码与颜色**：
- 使用 `lstlisting` + `style=shell` 排版命令行；`shellinbox` 用于框内嵌套。
- 已定义颜色：`gitorange`、`gitdark`、`codebg`、`codeframe`、`tipgreen`、`warnred`、`infoblue`、`sectioncolor`、`linkcolor`、`imgborder`。
- `\figurename=图`，`\tablename=表`。

**铁律**：新内容必须复用上述宏与环境。新增宏须充分论证且命名避免冲突。

## 5. 内容撰写规范

**章节结构（必须与 .tex 保持一致）**：
1. 版本控制与 Git 概述
2. Git 安装与配置
3. Git 基础操作
4. 分支与合并
5. 远程仓库与 GitHub
6. VS Code 中的 Git 集成
7. 实战工作流
8. 新手避坑指南
9. 附录：常用命令速查表

**开篇已有**：三个核心类比 / Git 的设计哲学：四个关键词 / 本教程的学习路径图 / 动手实验 0：验证环境就绪。

**写作要求**：
- **详尽**：每节包含概念解释、操作步骤、示例。
- **通俗**：善用类比（如“快照”“时光机”），沿用书中已有的“三个核心类比”与“设计哲学”。
- **图文并茂**：每个操作环节必须引用 `images/` 中的真实截图（`\vscodeimg` 等），或明确标注“需补充截图 `NN-xxx.png`”。
- **循序渐进**：设置“动手实验”小节，让读者跟随操作。

## 6. 事实核查与溯源规范（最重要）

- **命令验证**：任何 `git`、`gh` 命令必须先在本机执行验证（`git <cmd> --help` 或真实运行、`git --version`），确认输出后方可写入；禁止编造命令行为或输出。
- **UI 验证**：VS Code 菜单路径、按钮位置必须在本机打开确认，注明验证时的 VS Code 版本号。
- **来源标注**：每章/每节末尾用 `infobox` 或脚注列出参考来源：
  - Git 官方文档：https://git-scm.com/doc
  - Pro Git 2e 中文版：https://git-scm.com/book/zh/v2
  - VS Code Source Control 文档：https://code.visualstudio.com/docs/sourcecontrol
  - GitHub Docs：https://docs.github.com
  - 本项目素材：`sc-*.md`
- **不确定内容**：无法验证的内容标记 `【待核实】`，不得写入正文。
- **截图真实**：必须为本机实拍或官方文档原图（注明来源），禁止 AI 生成伪造截图。

## 7. 写作流程（每节/每章标准流程）

1. **核实事实**：本机实测 + 查阅官方文档。
2. **起草正文**：复用现有宏与风格，引用真实截图。
3. **配图处理**：引用 `images/` 现有图，或按命名规则新增截图并放入目录。
4. **编译验证**：`xelatex` 无错、图片路径存在、交叉引用无警告。
5. **自查清单**：对照第 8 节 DoD 逐项检查。

## 8. 审校自查清单（DoD）

- [ ] `xelatex` 编译通过，`.log` 无 fatal error。
- [ ] 引用的每张图片真实存在于 `images/`。
- [ ] 每个命令均经过本机实测。
- [ ] 每章有来源标注。
- [ ] 中文风格直接、电报式，无 emoji（LaTeX 中 `fontawesome5` 图标除外）。
- [ ] 未引入与现有宏冲突的新定义。

## 9. 语言风格

- 中文撰写，直接、不做作，避免 AI 腔。
- 不用 emoji；LaTeX 内允许使用 `\faLightbulb` 等 `fontawesome5` 符号作为排版元素。
- 技术术语首次出现时附英文原词，后续直接使用中文或约定缩写。
