# Plan: extensions-usage-tutorial

## TL;DR (For humans)

为 `Git_VSCode_Tutorial.tex` 第 6 章「推荐扩展」节补充 5 个推荐的 VS Code Git 扩展的完整使用教程。每个扩展新增一个 `\subsection`（简介 + 安装 + 核心用法 + 实用技巧），从 Marketplace/GitHub 获取官方截图，编译两次验证通过。

---

## Scope

### IN

- 为表格 `tab:recommended-extensions` 中列出的 5 个扩展各新增 `\subsection` 教程：
  - GitLens（行 2447 已有提示段落，本次扩展为完整子节，保留原有提示）
  - GitHub Pull Requests（全新）
  - GitHub Issues（全新）
  - Git Graph（全新）
  - Git History（全新）
- 每个子节含：简介、安装步骤、核心用法（命令面板入口 + 界面操作）、实用技巧
- 从 Marketplace / GitHub README 下载官方截图到 `images/`
- 中文正文，技术标识符/命令用英文

### OUT

- 不新增章节（`\chapter`）
- 不新增 tcolorbox 环境
- 不修改现有表格（行 2430-2445）和现有 GitLens 提示（行 2447-2453）
- 不修改 `images/` 中已有文件

### Risk: dirty worktree

- `lsp_diagnostics` 显示 `.tex` 文件有 27 个 undefined reference 警告（来自上次编译残留的 .aux），非本次引入
- 编译前需清理残留 xelatex 进程（`taskkill /f /im xelatex.exe`）

---

## Approach

1. 获取截图：从各扩展的 Marketplace 页面 / GitHub README 抓取官方展示截图
2. 撰写 LaTeX 内容：按规范为每个扩展写 `\subsection`，含 `\vscodeimg`
3. 插入 .tex：在行 2453（GitLens 提示 `\end{itemize}`）之后、行 2455（`\subsection*{动手实验 6}`）之前插入
4. 编译验证：xelatex 两次，确认无致命错误、交叉引用解析正确

---

## Notepad

### File: Git_VSCode_Tutorial.tex

#### Insertion point (after line 2453, before line 2455)

```latex
% Line 2453: \end{itemize}
% Line 2454: (blank)
% Line 2455: \subsection*{动手实验 6：VS Code中完成完整Git工作流}
```

New content goes between line 2453 and line 2455.

### Image numbering

Next available: 28. Allocate:

| # | Extension | Description |
|---|-----------|-------------|
| 28 | GitLens | blame annotation + CodeLens overview |
| 29 | GitLens | Commit Graph view |
| 30 | GitHub Pull Requests | PR list / review interface |
| 31 | Git Graph | branch timeline graph |
| 32 | Git History | file history view |

### Extension details (from librarian research)

#### GitLens
- Install: search "GitLens" in Extensions (`Ctrl+Shift+X`)
- Key usage:
  - Inline Blame: appears automatically at end of each line
  - CodeLens: shows author + commit at top of functions/classes
  - File History: right-click file → "Open File History" or command palette `GitLens: Open File History`
  - Commit Graph: command palette `GitLens: Show Commit Graph`
  - Branch Comparison: command palette `GitLens: Compare References`
  - Toggle: `GitLens: Toggle Line Blame` / `GitLens: Toggle Git CodeLens`

#### GitHub Pull Requests
- Install: search "GitHub Pull Requests" (author: GitHub)
- Prerequisite: must sign in to GitHub account (built-in Accounts in VS Code)
- Key usage:
  - Sidebar icon appears as GitHub logo → shows PR list
  - View PR details, diffs, comments
  - Create PR: `GitHub Pull Requests: Create Pull Request`
  - Review: checkout PR branch, leave comments inline
  - Checkout PR: click "Checkout" on any PR in the list

#### GitHub Issues
- Install: search "GitHub Issues" (author: GitHub)
- Key usage:
  - Sidebar: GitHub Issues view shows open issues
  - Create issue: `GitHub Issues: Create Issue from Selection` or button in sidebar
  - Link issues: reference `#issue-number` in commit messages

#### Git Graph
- Install: search "Git Graph" (author: mhutchie)
- Launch: status bar button or `Git Graph: View Git Graph` in command palette
- Key usage:
  - View: scrollable branch timeline with colored branch lines
  - Right-click commit → Cherry Pick / Revert / Checkout / Create Branch / Merge
  - Right-click branch → Checkout / Delete / Merge / Push / Rebase
  - Click a commit: see details + file changes in bottom panel
  - CTRL+Click second commit: compare any two commits
  - Find: `Ctrl+F` in graph view to search commits

#### Git History
- Install: search "Git History" (author: Don Jayamanne)
- Key usage:
  - File History: open file → command palette `Git: View File History`
  - Line History: open file → command palette `Git: View Line History`
  - Branch/Commit comparison: `Git: View Branch History` or `Git: View Commit History`
  - IMPORTANT: must have the target file open before running commands

---

## Todos

- [x] 1. `images/`: Download extension screenshots from official sources — acquire 28-gitlens-blame.png, 29-gitlens-graph.png, 30-gh-pr.png, 31-git-graph.png, 32-git-history.png and place in images/

- [x] 2. `Git_VSCode_Tutorial.tex`: Insert `\subsection{GitLens 详细使用指南}` after line 2453 — write introduction, install steps, core usage (inline blame, CodeLens, file history, commit graph, branch comparison, toggle commands), tips. Include `\vscodeimg` for screenshots 28 and 29. Verify existing GitLens tips paragraph (lines 2447-2453) is preserved above.

- [x] 3. `Git_VSCode_Tutorial.tex`: Insert `\subsection{GitHub Pull Requests 使用指南}` — write introduction, install, GitHub sign-in prerequisite, core usage (sidebar, view PR, create PR, review, checkout), tips. Include `\vscodeimg` for screenshot 30.

- [x] 4. `Git_VSCode_Tutorial.tex`: Insert `\subsection{GitHub Issues 使用指南}` — write introduction, install, core usage (sidebar, create issue, link in commits), tips. No screenshot (text-only).

- [x] 5. `Git_VSCode_Tutorial.tex`: Insert `\subsection{Git Graph 使用指南}` — write introduction, install, launch methods, core usage (branch timeline, right-click actions, commit details, comparison, find), tips. Include `\vscodeimg` for screenshot 31.

- [x] 6. `Git_VSCode_Tutorial.tex`: Insert `\subsection{Git History 使用指南}` — write introduction, install, core usage (file history, line history, branch comparison), tips (must open file first). Include `\vscodeimg` for screenshot 32.

- [x] 7. `Git_VSCode_Tutorial.tex`: Compile with xelatex twice — verify no fatal errors, all `\ref{fig:ext-*}` resolve to correct figure numbers. Verify table of contents, page count reasonable.

## Final verification wave

- [x] F1. `git diff` review: confirm no unintended changes to existing sections, tables, or images. All modifications are within the `\section{推荐扩展}` scope (lines 2426-2453 + new content before line 2455). Confirm existing GitLens tips paragraph untouched.

- [x] F2. `xelatex` log review: compile log contains zero `Fatal error` or `Error: Undefined control sequence`. Overfull/Underfull hbox warnings are acceptable. Check that `\ref{fig:ext-gitlens-blame}`, `\ref{fig:ext-gitlens-graph}`, `\ref{fig:ext-gh-pr}`, `\ref{fig:ext-git-graph}`, `\ref{fig:ext-git-history}` all resolve (no `??`).

- [x] F3. Structure check: verify 5 new `\subsection` headings appear in the generated PDF table of contents under Chapter 6. Verify the section ordering: GitLens → GitHub Pull Requests → GitHub Issues → Git Graph → Git History → 动手实验 6.

- [x] F4. Content sanity: each subsection has (a) a `\subsection{...}` header, (b) a brief intro sentence, (c) install steps ending with Install button, (d) 3-5 core usage items each with command palette entry or UI action, (e) a tips/hint sentence at the end.

---

## Dependency matrix

```
C1 (screenshots) ──┐
                    ├──> C2 (write content) ──> C3 (integrate) ──> C4 (compile)
                    │
   (none blocking)  │   todos 1 ─────> todos 2-6 ──> todo 7
```

- Todo 1 (screenshots) is independent — can run in parallel with content writing; screenshots are referenced but writing can use placeholder paths
- Todos 2-6 (subsections) are independent of each other — all insert into the same insertion zone
- Todo 7 (compile) depends on todos 1-6

---

## Must-NOT-Have

- New `\chapter`, `\section*`, or tcolorbox environment definitions
- Modification to lines 2430-2445 (table) or lines 2447-2453 (GitLens tips)
- Deletion of any existing `warnbox` instances
- Changes to any file outside `Git_VSCode_Tutorial.tex` and `images/`
- Git write operations (commit, push, etc.)
