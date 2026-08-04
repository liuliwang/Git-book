# Learnings — extensions-usage-tutorial

Conventions, patterns, and successful approaches discovered during work on this plan.

_Auto-scaffolded by /start-work. Append new entries below - never overwrite._

---

## 2026-08-03 — Inserted 5 extension usage subsections into Git_VSCode_Tutorial.tex

- Inserted 5 new `\subsection{...}` blocks into Chapter 6 (`\section{推荐扩展}`), between the existing GitLens tips list (line 2453) and `\subsection*{动手实验 6...}` (now at line 2555).
- Subsections added (in order):
  1. `\subsection{GitLens 详细使用指南}` (line 2455)
  2. `\subsection{GitHub Pull Requests 使用指南}` (line 2476)
  3. `\subsection{GitHub Issues 使用指南}` (line 2494)
  4. `\subsection{Git Graph 使用指南}` (line 2510)
  5. `\subsection{Git History 使用指南}` (line 2529)
- Each subsection contains: intro sentence, install steps, 3-5 core usage items with command-palette entries, and a tips sentence.
- Image files 28-32 did not exist at write time; all 5 planned `\vscodeimg` references were written as `% TODO-IMG:` placeholders:
  - `images/28-gitlens-blame.png` (fig:ext-gitlens-blame)
  - `images/29-gitlens-graph.png` (fig:ext-gitlens-graph)
  - `images/30-gh-pr.png` (fig:ext-gh-pr)
  - `images/31-git-graph.png` (fig:ext-git-graph)
  - `images/32-git-history.png` (fig:ext-git-history)
- Lines 2430-2453 (extension table + GitLens tips list) were left untouched.
- No tcolorbox environments were used in the new content.
- All commands are quoted with `\texttt{...}`; lists use `\begin{itemize}[leftmargin=2em]`.
- LSP diagnostics show only pre-existing undefined-reference errors (from `\ref` needing a second xelatex pass), none introduced by this edit.
## 2026-08-03 �� Todo 1: Web-sourced extension screenshots (28�C32)

All 5 images downloaded into images/, each verified as a real PNG (magic bytes 89 50 4E 47; content checked via look_at):

| File | Source |
|------|--------|
| 28-gitlens-blame.png | https://raw.githubusercontent.com/gitkraken/vscode-gitlens/main/images/docs/current-line-blame.png (official repo, native PNG) |
| 29-gitlens-graph.png | https://raw.githubusercontent.com/gitkraken/vscode-gitlens/main/images/docs/commit-graph.png (official repo, native PNG) |
| 30-gh-pr.png | https://raw.githubusercontent.com/microsoft/vscode-pull-request-github/main/.readme/overview.png (official repo, native PNG; shows PR list + review UI) |
| 31-git-graph.png | Frame 127 of https://raw.githubusercontent.com/mhutchie/vscode-git-graph/develop/resources/demo.gif �� GIF��PNG frame conversion via System.Drawing (only official source; README/marketplace/VSIX contain no PNG screenshot) |
| 32-git-history.png | Frame 48 of https://raw.githubusercontent.com/DonJayamanne/gitHistoryVSCode/main/images/fileHistoryCommandv3.gif �� GIF��PNG frame conversion via System.Drawing (same situation) |

Key learnings:
- Marketplace gallery API: POST https://marketplace.visualstudio.com/_apis/public/gallery/extensionquery with flags=950 + Accept: application/json;api-version=3.0-preview.1 �� but NO extension exposes screenshot assets there; only README GIFs serve as demos. VSIX packages also contain no screenshots (checked githistory 0.6.20, git-graph 1.30.0).
- Wiki images (git-graph) must be fetched via https://github.com/<owner>/<repo>/wiki/resources/<file>.png (raw.githubusercontent.com/wiki path returns 404).
- GIF frame extraction in PS 5.1: use [System.Drawing.Imaging.FrameDimension]::Time and parenthesize arithmetic inside array literals �� `@(0, $mid, $count - 1)` parses as (array)-1 due to comma precedence; write `($count - 1)`.
- Expand-Archive rejects .vsix; copy to .zip first.
- This model cannot view images; look_at tool is the verification path for screenshot content.

## 2026-08-03 — Replaced 5 TODO-IMG placeholders with actual \vscodeimg commands

- All 5 `% TODO-IMG:` placeholder comment lines in `Git_VSCode_Tutorial.tex` were replaced with active `\vscodeimg{...}{...}{...}` commands.
- Replacements made (line numbers reflect post-edit state):
  - Line 2470: `\vscodeimg{images/28-gitlens-blame.png}{GitLens 行内 Blame 与 CodeLens 显示}{fig:ext-gitlens-blame}`
  - Line 2472: `\vscodeimg{images/29-gitlens-graph.png}{GitLens 提交图（Commit Graph）}{fig:ext-gitlens-graph}`
  - Line 2490: `\vscodeimg{images/30-gh-pr.png}{GitHub Pull Requests 侧边栏中的 PR 列表}{fig:ext-gh-pr}`
  - Line 2525: `\vscodeimg{images/31-git-graph.png}{Git Graph 分支时间线视图}{fig:ext-git-graph}`
  - Line 2543: `\vscodeimg{images/32-git-history.png}{Git History 文件历史视图}{fig:ext-git-history}`
- Verification: `grep "TODO-IMG"` returns zero matches; `grep "\\vscodeimg{images/"` confirms all 5 commands present.
- No other content was modified.

## 2026-08-03 — Double xelatex compile PASSED (5 new figures resolved)

- Ran `taskkill /f /im xelatex.exe` first (zombie-process trap), then two sequential xelatex passes with `-interaction=nonstopmode`.
- Both passes: exit code 0; output `Git_VSCode_Tutorial.pdf` (89 pages, ~3.86 MB, regenerated 16:20:33).
- Final log verification (grep on Git_VSCode_Tutorial.log): zero matches for `Fatal error`, `Emergency stop`, `Undefined control sequence`, and `LaTeX Warning: Reference .*fig:ext-`. Also no "There were undefined references" warning on final pass.
- All 5 new labels resolved to real figure numbers in .aux: fig:ext-gitlens-blame=6.9, fig:ext-gitlens-graph=6.10, fig:ext-gh-pr=6.11, fig:ext-git-graph=6.12, fig:ext-git-history=6.13.
- Overfull/Underfull hbox warnings acceptable per project convention; no action needed.
- Lesson: `fatal: refusing to` in log is document body text (Git error example), not a compile error; first pass legitimately shows undefined refs, second pass is authoritative.

## 2026-08-03 — F4 Content Sanity Review: VERDICT = APPROVE (all 5 subsections)

Reviewed `Git_VSCode_Tutorial.tex` lines 2455-2545 (5 subsections) against criteria (a)-(f). All PASS.

| Subsection (line) | (a) header | (b) intro | (c) install | (d) 3-5 items | (e) tips | (f) images | Commands verified |
|---|---|---|---|---|---|---|---|
| GitLens (2455) | ✓ | ✓ | ✓ Ctrl+Shift+X / GitLens / Install | 5 items (2 UI + 4 \texttt cmds) | ✓ | 2: 28 fig:ext-gitlens-blame, 29 fig:ext-gitlens-graph | Show Commit Graph / Open File History / Toggle Line Blame / Toggle Git CodeLens — all real |
| GitHub Pull Requests (2476) | ✓ | ✓ | ✓ + GitHub: Sign in login step present (required) | 4 items (3 UI + 1 cmd) | ✓ | 1: 30 fig:ext-gh-pr | Create Pull Request — real; Sign in — real |
| GitHub Issues (2494) | ✓ | ✓ | ✓ Ctrl+Shift+X / GitHub Issues / Install | 4 items (3 UI + 1 cmd) | ✓ | 0 (as required) | Create Issue from Selection — real |
| Git Graph (2510) | ✓ | ✓ | ✓ Ctrl+Shift+X / Git Graph / Install | 5 items (1 cmd + 4 UI actions) | ✓ | 1: 31 fig:ext-git-graph | View Git Graph — real |
| Git History (2529) | ✓ | ✓ | ✓ Ctrl+Shift+X / Git History / Install | 4 items (all \texttt cmds) | ✓ | 1: 32 fig:ext-git-history | View File/Line/Branch/Commit History — all real |

Evidence:
- grep `\subsection{Git...` → 5 matches exactly at 2455/2476/2494/2510/2529.
- grep `\vscodeimg{images/(28-32)-` → 5 matches at 2470/2472/2490/2525/2543; all 5 PNGs exist in images/ (sizes 12KB-527KB).
- All 11 \texttt commands grep-confirmed; all match real extension command titles.
- No tcolorbox environments in new content (lines 2455-2545 = plain prose + itemize + \vscodeimg only).
- Minor notes (non-blocking): GitHub PR install search uses full official name "GitHub Pull Requests and Issues" (more accurate than short name); `GitLens: Open File History` was renamed in newer GitLens versions but is historically correct and clear for beginners.

## 2026-08-03 — F3 Final Wave VERDICT: APPROVE

- Method: `pdftotext -layout` on compiled `Git_VSCode_Tutorial.pdf` (89 pages, 3,859,904 bytes) → extracted text to temp, grepped headings + figure captions.
- TOC (目录) lists all 5 new subsections under 6.8 推荐扩展, in exact order with page numbers: 6.8.1 GitLens 详细使用指南 (p.68), 6.8.2 GitHub Pull Requests 使用指南 (p.69), 6.8.3 GitHub Issues 使用指南 (p.70), 6.8.4 Git Graph 使用指南 (p.71), 6.8.5 Git History 使用指南 (p.71).
- Body text confirms the same order in document flow (extracted line numbers 2737 → 2785 → 2817 → 2837 → 2863), all inside Chapter 6 `\section{推荐扩展}` (6.8).
- 动手实验 6：VS Code 中完成完整 Git 工作流 (unnumbered subsection) appears at extracted line 2888, strictly AFTER all 5 new subsections. ✓
- Figures verified near their subsections: 图 6.9 GitLens 行内 Blame 与 CodeLens 显示 (in 6.8.1), 图 6.10 GitLens 提交图（Commit Graph）(in 6.8.1), 图 6.11 GitHub Pull Requests 侧边栏中的 PR 列表 (in 6.8.2), 图 6.12 Git Graph 分支时间线视图 (in 6.8.4), 图 6.13 Git History 文件历史视图 (in 6.8.5). GitHub Issues (6.8.3) correctly has no figure — matches plan (5 figures for 5 subsections, GitLens owns 2). ✓
- No missing headings, no order violations, no orphan figures. F3 structure check PASSED.

## 2026-08-03 — Final Wave F2 (xelatex log review): VERDICT = APPROVE

F2 review of `Git_VSCode_Tutorial.log` (final/second pass) and `Git_VSCode_Tutorial.pdf`:

- **Errors**: 0 matches for `Fatal error` / `Emergency stop` / `Undefined control sequence` / `There were undefined references` in the final log.
- **Undefined refs**: zero "There were undefined references" warning on final pass; no `??` anywhere.
- **5 new figure labels resolved** (from .aux `\newlabel` entries): fig:ext-gitlens-blame=6.9, fig:ext-gitlens-graph=6.10, fig:ext-gh-pr=6.11, fig:ext-git-graph=6.12, fig:ext-git-history=6.13 — all match expected numbers.
- **PDF output**: `Output written on Git_VSCode_Tutorial.pdf (89 pages)`; file = 3,859,904 bytes (~3.7 MB), LastWriteTime 2026/8/3 16:20:33, matching the log header timestamp ("3 AUG 2026 16:20") — confirms the log is from the final pass that produced this PDF.
- **`fatal: refusing to`** present at log line 1847 as typeset document body text (Git error example), not a compile error — correctly not flagged.
- Overfull/Underfull hbox warnings: accepted per project convention.

**Verdict: APPROVE.** Plan gate satisfied. No recompile, no file modifications, no git operations performed during review.

## 2026-08-03 - Final Wave F1 (git diff review): VERDICT = APPROVE

F1 review of change surface via `git status --short`, `git diff --stat`, `git diff Git_VSCode_Tutorial.tex`, `git diff Git_VSCode_Tutorial.toc`.

### Change surface (all files)
- M  Git_VSCode_Tutorial.tex  -> 92 insertions, 0 deletions (single hunk @@ -2452,6 +2452,98 @@)
- ?? images/28-gitlens-blame.png, 29-gitlens-graph.png, 30-gh-pr.png, 31-git-graph.png, 32-git-history.png  (5 NEW, untracked)
- M  .omo/boulder.json + .omo/run-continuation/*.json + new .omo/{plans,notepads,drafts}  (orchestration state, allowed)
- M  Git_VSCode_Tutorial.{aux,log,out,pdf,toc}  (build byproducts of mandated double-compile; see note 1)
- M  AGENTS.md  (stat-dirty only; see note 2)

### Scope verification
1. **.tex diff is a pure insertion**: one hunk, 92 added lines, 0 removed. Content = 5 new `\subsection` blocks (GitLens 详细使用指南 / GitHub Pull Requests 使用指南 / GitHub Issues 使用指南 / Git Graph 使用指南 / Git History 使用指南) + 5 `\vscodeimg` lines (fig:ext-gitlens-blame / fig:ext-gitlens-graph / fig:ext-gh-pr / fig:ext-git-graph / fig:ext-git-history). Inserted exactly between the GitLens tips list (`\end{itemize}`, old line 2453) and `\subsection*{动手实验 6...}` (old line 2455). No existing text modified, no deletions.
2. **Table + tips list byte-identical**: `tab:recommended-extensions` table (current lines 2430-2445) and GitLens tips list (2447-2453) appear in the diff ONLY as context lines (verified by reading current file). Not +/- lines.
3. **Images**: only 5 new files added; existing 01-26-*.png and merge-conflict.png untouched (git status -- images/ shows only the 5 ??).
4. **sc-*.md**: none modified.
5. **No other chapter/section changed**: single .tex hunk proves all other 3000+ lines identical; .toc diff confirms the ONLY structural addition is subsections 6.8.1-6.8.5 (rest of TOC entries unchanged, page numbers shifted by reflow).

### Notes (not scope violations in substance)
1. **Build artifacts (aux/log/out/pdf/toc)**: unavoidable byproducts of the plan-mandated "compile twice" step. .toc diff verified to contain ONLY the 5 new subsection entries + reflowed page numbers; .pdf grew 3,053,615 -> 3,859,904 bytes, consistent with 5 new screenshots embedded. These are derived output, not hand-modified content.
2. **AGENTS.md**: shows ` M` in status but `git diff` AND `git diff --ignore-cr-at-eol` are EMPTY -> zero content change, line-ending-only touch (CRLF normalization). Not a content modification.

### Verdict
APPROVE. The only substantive non-orchestration changes are exactly the intended ones: Git_VSCode_Tutorial.tex (92-line pure insertion inside 推荐扩展) + 5 new images (28-32). No existing sections/tables/images/sc-*.md were modified. No deletions in .tex. Read-only review; no git write operations, no file modifications (except this notepad append).
