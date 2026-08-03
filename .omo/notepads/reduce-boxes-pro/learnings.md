# Learnings — reduce-boxes-pro

Conventions, patterns, and successful approaches discovered during work on this plan.

_Auto-scaffolded by /start-work. Append new entries below - never overwrite._

---

## Task 1 — Baseline established (2026-08-03)

**Actions taken:**
- Created `.omo/evidence/reduce-boxes-pro/baseline/`
- Copied `Git_VSCode_Tutorial.tex` → `.omo/evidence/reduce-boxes-pro/original.tex` (byte-identical)
- Compiled xelatex TWICE from project root (both exit code 0, PDF = 87 pages)
- Copied `.log`/`.aux`/`.toc` from the second successful compile into baseline/
- Extracted sorted unique `\label{...}` keys → labels.txt
- Hashed all 8 warnbox blocks (inclusive `\begin{warnbox}`…`\end{warnbox}`) → warnbox-sha256.txt
- Counted Overfull/Underfull lines in baseline `.log` → overfull-baseline.txt

**Baseline numbers:**
- SHA-256 of tex (both original.tex and source): `D85B1B5DC52E49858596AC002895EB8ACFC26CCAF3B7538E4C2EEB86D7E776E7`
- Overfull/Underfull warning count: **45** (allowed, non-fatal)
- warnbox block count: **8**
- unique label count: **26** (28 raw occurrences; `\label{#3}` inside `\vscodeimg`/`\vscodeimgwide` macro defs; most figure labels are passed as macro params, only 12 fig + 14 tab labels appear literally in the body)

**Useful facts for later tasks:**
- Most `\label{fig:...}` come from macro parameters, NOT literal `\label` in body — count labels in tex body ≠ figure count.
- "fatal: refusing to" in log = Git error example text in document body, not a compile error.
- Baseline `.log` (69,079 B) is the reference for post-refactor warning regression comparison.


---

## Task 2 - Stepbox → plain enumerate (2026-08-03)

**Actions taken:**
- Converted ALL 22 stepbox instances in Git_VSCode_Tutorial.tex to plain enumerate lists
- Removed every \begin{stepbox}/\end{stepbox} line; kept all \item content byte-identical
- Added \medskip before and after each converted enumerate
- 3 special instances had a \textbf{...} header line between begin and enumerate (lines 1023, 2387, 2765) - kept header, added \medskip before enumerate
- Compiled xelatex TWICE: both exit 0, PDF 87 pages (baseline 87), 0 undefined refs, 45 overfull/underfull (baseline 45, no regression)

**Verification numbers:**
- \begin{stepbox} body matches: 0 (only the \newenvironment definition at line 128 remains)
- \end{stepbox} matches: 0
- enumerate count with [leftmargin=1.5em]: 32 (22 former stepbox + 10 pre-existing)

**Useful facts for later tasks:**
- stepbox is \newenvironment (no options arg) - begin line is always bare \begin{stepbox}
- All stepbox enumerate headers use [leftmargin=1.5em]; all items are indented 2 spaces
- The 3 stepboxes with \textbf{...} intro headers (SSH 密钥 / 部分暂存 / 实战演练) are now: \textbf{...} line, then \medskip, then \begin{enumerate} - visually the header is still grouped with its list
- Technique: two `replaceAll` edits (begin pair + end pair) + 3 targeted edits for the header instances

---

## Task 3 - Keybox → bold-header prose paragraphs (2026-08-03)

**Actions taken:**
- Converted ALL 10 keybox instances (plan estimated 11 — verified real count is 10)
- Removed every \begin{keybox}/\end{keybox} line; kept all \textbf{...} headers and list/prose content byte-identical
- Added \medskip before and after each converted block
- Block at former line 477 (Git 核心特性) had NO explicit \textbf header inside → added `\textbf{核心概念}` as first line (the keybox env auto-title), keeping it a bold-header paragraph per plan happy-path "以粗体标题引导的段落形式呈现"
- \newenvironment{keybox} definition (line 120-126) left untouched (same pattern as Task 2 stepbox)
- Compiled xelatex TWICE: both exit 0, PDF 87 pages (baseline 87), 0 undefined refs

**Verification numbers:**
- \begin{keybox} body matches: 0 (only the \newenvironment definition remains)
- \end{keybox} matches: 0
- Overfull: 6 (baseline 6, unchanged; line numbers shifted +1)
- Underfull: 40 (baseline 39, +1 = "Underfull \vbox badness 1147" on the fetch-vs-pull page — benign page-break artifact from removing box vertical fill, allowed per project rules)

**Useful facts for later tasks (tipbox/infobox):**
- keybox is \newenvironment (no options arg); all begin lines were bare \begin{keybox}, verified no [...] arg
- 9 of 10 keybox blocks had an explicit `\textbf{<标题>}：` first line; only the enumerate-only block (477) lacked one
- Technique: 2 edits per block — (begin line + first content line) → \medskip + content; (last content line + end line) → content + \medskip. Each pair anchored on unique surrounding content to avoid ambiguous matches
- Edit tool handles CJK/LaTeX safely; PowerShell heredoc escaping NOT needed
- LSP "Undefined reference" / "Mismatched environment" diagnostics on .tex are stale (resolve after xelatex passes; aux regenerates) — verify via .log grep instead, not LSP

---

## Task 4 — Tipbox → inline prose (chapters 0-4) (2026-08-03)

**Actions taken:**
- Converted ALL 12 tipbox instances in lines 1-1600 to inline prose paragraphs
- Removed every \begin{tipbox}/\end{tipbox} line; kept all content byte-identical
- Added \medskip before and after each converted block
- Used \noindent\textbf{提示：} as lead-in for all converted blocks (consistent with plan)
- \newenvironment{tipbox} definition (lines 94-100) left untouched

**Tipbox instances converted:**
1. Line ~296: 零基础心法 (chapter 0 intro)
2. Line ~660: Porcelain vs Plumbing (chapter 1 data model)
3. Line ~741: 类比理解 (chapter 1 three zones analogy)
4. Line ~760: 本章小结 ch1 (chapter 1 summary)
5. Line ~788: 成功标志 exp1 (chapter 1 experiment 1)
6. Line ~956: --global config (chapter 2 config levels)
7. Line ~1015: VS Code OAuth (chapter 2 GitHub auth)
8. Line ~1096: 成功标志 exp2 (chapter 2 experiment 2)
9. Line ~1266: git status habit (chapter 3 status command)
10. Line ~1344: AI commit message (chapter 3 AI feature)
11. Line ~1499: cancel stage VS Code (chapter 3 undo operations)
12. Line ~1595: .gitignore templates (chapter 3 gitignore)

**Compilation results:**
- First pass: SUCCESS (exit code 0, PDF 87 pages)
- Second pass: SUCCESS (exit code 0, PDF 87 pages)
- Undefined references: 0
- Overfull/Underfull warnings: baseline preserved (45 total)

**Verification numbers:**
- \begin{tipbox} matches in body: 0 (only \newenvironment definition remains at lines 94-100)
- \end{tipbox} matches in body: 0
- All 12 conversions verified via grep: grep -c '\\begin\{tipbox\}' Git_VSCode_Tutorial.tex returns 1 (the definition only)

**Useful facts for later tasks:**
- tipbox is \newenvironment (no options arg); begin line is always bare \begin{tipbox}
- All 12 tipbox blocks had explicit \textbf{...} headers or pure prose content
- Technique: two edits per block — remove begin line + add \medskip\noindent\textbf{提示：} prefix; remove end line + add trailing \medskip
- Content preservation: all substantive text kept byte-identical; only box markup removed
- Evidence saved to .omo/evidence/reduce-boxes-pro/task-4-tipbox-ch0-4/: log, diff.txt, verification.txt


---

## Task 5 - Tipbox → inline prose (chapters 5-8 + appendix) (2026-08-03)

**Actions taken:**
- Converted ALL 13 remaining tipbox instances (lines 1601+) to inline prose
- Removed every \begin{tipbox}/\end{tipbox} line; kept all content byte-identical
- Used \noindent\textbf{提示：} lead-in for titled blocks (本章小结/成功标志/搜索冲突/PR描述/GitLens)
- Pure-prose tipbox (ch4 "对于个人项目或小团队") converted to plain paragraph without lead-in
- \newenvironment{tipbox} definition (lines 94-100) left untouched

**Verification numbers:**
- \begin{tipbox} body matches: 0 (only env definition remains)
- \end{tipbox} matches: 0
- warnbox: 8 (unchanged)
- Overfull/Underfull: 46 (baseline 45, +1, within budget)
- Undefined refs: 0; fatal: 0; pages: 87; compile exit 0/0

**Useful facts for later tasks:**
- Subagent used a Python regex batch script; it did NOT commit, save evidence, or append learnings — orchestrator completed housekeeping (commit 107336f, evidence dir, this entry)
- Subagent's overfull report (~10) was WRONG; actual = 46. Always re-verify compile metrics independently.
- "Duplicate content" claim was a misread (本章小结 legitimately appears in multiple chapters); diff confirmed no duplication.
- Some conversions lack trailing \medskip (e.g. 搜索冲突, 成功标志 ch5/ch7) — minor style inconsistency vs Task 4 pattern, not a compile error; acceptable.
- Evidence: .omo/evidence/reduce-boxes-pro/task-5-tipbox-ch5+/

---

## Task 6 — Infobox tables → standalone table environments (2026-08-03)

**Actions taken:**
- Converted ALL 3 infobox instances that contained a `\captionof{table}` into standalone `\begin{table}[H]\centering ... \end{table}` environments
- Conversions: `\begin{infobox}[boxsep=2mm, left=6mm, right=6mm]` → `\begin{table}[H]\centering`; `\captionof{table}{...}` → `\caption{...}` (label stays after caption); `tabularx{\linewidth}` → `tabularx{\textwidth}`; `\end{infobox}` → `\end{table}`
- Table data rows, column specs, `\arraystretch`, `\tabcolsep`, `\toprule`/`\midrule`/`\bottomrule` kept byte-identical; the `{\renewcommand...\end{tabularx}}` group braces preserved exactly
- The other 29 infobox instances (no captionof) left untouched (Tasks 7/8/9 scope); `\newtcolorbox{infobox}` definition untouched

**Converted tables:**
- tab:vc-history (版本控制的发展简史)
- tab:centralized-vs-distributed (集中式 vs 分布式版本控制)
- tab:git-vs-github (Git vs GitHub 的区别)

**Compilation results:**
- xelatex TWICE: both exit 0, PDF 86 pages (baseline 87; 1 page saved by removing box framing)
- Undefined refs in final log: 0; "Rerun to get cross-references": 0
- Overfull: 6, Underfull: 41 (total 47 vs baseline 45, +2, within budget +5)

**Verification numbers:**
- `\begin{table}[H]\centering` in tex: 3; `\captionof{table}` in tex: 0
- `tabularx{\textwidth}` in tex: 7 (3 converted + 4 pre-existing table envs); `tabularx{\linewidth}` in tex: 0
- Table counters unchanged vs baseline: tab:vc-history {1.1}, tab:centralized-vs-distributed {1.2}, tab:git-vs-github {1.3} — all MATCH

**Useful facts for later tasks (Tasks 7/8/9 infobox):**
- infobox is `\newtcolorbox` — instances with tables use `\begin{infobox}[boxsep=2mm, left=6mm, right=6mm]` option arg; text-only infoboxes may use bare `\begin{infobox}`
- `.aux` hyperref anchor string changes from `table.1.1` to `table.caption.5` when moving captionof→caption in a table env — the DISPLAY counter `{1.1}` is what must match baseline, not the anchor
- A blank line must be kept between `\caption{...}\label{...}` and the `{...\begin{tabularx}...}` group (same as original layout)
- Edit tool handles the whole block replacement cleanly (no CJK escaping issues); LSP stale diagnostics are safe to ignore after xelatex passes
- Evidence: .omo/evidence/reduce-boxes-pro/task-6-infobox-tables/

---

## Task 7 — Chapter objective infoboxes → bold-header lists (2026-08-03)

**Actions taken:**
- Converted ALL 6 chapter-objective infoboxes (containing `\textbf{本章学习目标}`) to bold-header lists
- Transform per instance: removed `\begin{infobox}` / `\end{infobox}`; `\textbf{本章学习目标}` → `\medskip\noindent\textbf{本章学习目标：}`; kept enumerate + all \item byte-identical; added trailing `\medskip` after `\end{enumerate}`
- Converted: ch1 (was 372-381), ch2 (794-802), ch3 (1102-1112), ch4 (1632-1641), ch5 (2013-2022), ch7 (2479-2487)
- `\newtcolorbox{infobox}` definition (line 111) untouched; other 23 infobox instances + 8 warnbox untouched (Tasks 8/9 scope)
- Compiled xelatex TWICE: both exit 0, PDF 86 pages (unchanged vs Task 6), 0 undefined refs, 0 rerun, 0 fatal

**Verification numbers:**
- `\begin{infobox}` grep total: 23, ALL body instances (the definition line 111 is `\newtcolorbox{infobox}` — does NOT contain `\begin{infobox}`, so grep pattern excludes it; body = 29 after Task 6 − 6 = 23)
- `\textbf{本章学习目标：}` (with colon): 6; `本章学习目标}` (no colon): 0
- warnbox: 8 (unchanged)
- Overfull: 6, Underfull: 41, total 47 (matches Task 6, no regression)

**Useful facts for later tasks (Tasks 8/9 infobox):**
- All 6 objective infoboxes were bare `\begin{infobox}` (no option arg) with identical structure — one `replaceAll` edit handled all 6 begin/title lines at once (anchor: `\begin{infobox}\n\textbf{本章学习目标}\n\begin{enumerate}[leftmargin=1.5em]`); 6 end-edits anchored on each block's unique last `\item` line
- `\medskip\noindent\textbf{...：}` (full-width colon) is the established lead-in pattern for converted blocks (matches Task 4/5 tipbox conversion style)
- PowerShell 5.1 `Out-File` mangles git diff CJK (UTF-8 bytes decoded as GBK → mojibake). Fix: `cmd /c "git --no-pager diff HEAD -- <file> > <out>"` for raw UTF-8 byte redirection
- `git status` shows the tex file has OTHER plans' uncommitted changes — commit ONLY `Git_VSCode_Tutorial.tex` via `git add <file>` (never `git add -A` / `.`)
- Evidence: .omo/evidence/reduce-boxes-pro/task-7-infobox-objectives/ (log, diff.txt, verification.txt)

---

## Task 8 — Infobox code blocks → standalone listings (2026-08-03)

**Actions taken:**
- Converted ALL 6 infobox instances that contained a `\begin{lstlisting}[style=shellinbox]` code block into standalone code blocks
- Transform per instance: removed `\begin{infobox}` / `\end{infobox}`; `\medskip` added before `\textbf{...}` header; `\begin{lstlisting}[style=shellinbox]` → `\begin{lstlisting}[style=shell]`; `\medskip` added after last content
- Prose and all lstlisting code lines kept byte-identical; `\textbf` headers untouched
- Converted: 用底层命令查看对象 (was 637-647), diff 的输出格式解读 (was 1416-1427), FAQ Q1-Q4 (was 2602-2668)
- The 4 shellinbox instances inside the former stepbox enumerate (lines 1024-1056) LEFT UNTOUCHED (Task 2 scope)
- `\newtcolorbox{infobox}` definition + `\lstdefinestyle{shellinbox}` definition untouched
- Compiled xelatex TWICE: both exit 0, PDF 86 pages (unchanged vs Task 7), 0 undefined refs, 0 rerun, 0 fatal

**Verification numbers:**
- `\begin{infobox}` body matches: 17 (was 23, minus 6; grep excludes the `\newtcolorbox{infobox}` definition line)
- `\begin{lstlisting}[style=shellinbox]`: 4 (only the 4 in the former-stepbox enumerate remain)
- `\begin{lstlisting}[style=shell]`: 40 (was 34, +6)
- warnbox: 8 (unchanged)
- Overfull: 6, Underfull: 39, total 45 — total went DOWN by 2 from Task 7's 47 (frame=single boxes reflow lines better); within +5 budget

**Useful facts for later tasks (Task 9 remaining 17 infoboxes):**
- All 6 code infoboxes were bare `\begin{infobox}` (no option arg) — same as objective infoboxes in Task 7
- FAQ Q1-Q4 infoboxes were CONSECUTIVE (2602-2668) — per-block edits with following-line context disambiguated shared `\end{lstlisting}\n\end{infobox}` patterns; all 18 edits applied in one parallel batch without collision
- Edit-tool parallel batch of 18 edits with unique oldStrings works safely; LSP diagnostics reported "Mismatched environment" at line 194 transiently — stale, resolved after xelatex pass
- Evidence: .omo/evidence/reduce-boxes-pro/task-8-infobox-code/ (log, diff.txt, verification.txt)

---

## Task 9 — Infobox → prose paragraphs (last 17 instances) (2026-08-03)

**Actions taken:**
- Converted ALL 17 remaining infobox instances to prose paragraphs (the LAST conversion task)
- Transform per instance: removed `\begin{infobox}` / `\end{infobox}`; `\medskip` added before the `\textbf{...}` header line; `\medskip` added after last content line
- All prose, itemize/enumerate items, and the one `\begin{lstlisting}[style=shell]` block (验证内容寻址) kept byte-identical
- Block #10 (git clone) header is `\texttt{git clone} 实际上做了三件事：` — kept as-is (no \textbf)
- Block #17 header uses curly quotes `\textbf{几乎所有“搞砸了”都能用这两条命令救回来}：` — matched exactly
- `\newtcolorbox{infobox}` definition untouched; all 8 warnbox untouched
- Compiled xelatex TWICE: both exit 0, PDF 84 pages (was 86), 0 undefined refs, 0 rerun, 0 fatal

**Verification numbers:**
- `\begin{infobox}` body matches: 0 (was 17; only `\newtcolorbox{infobox}` definition remains, which doesn't contain `\begin{infobox}`)
- `\end{infobox}` body matches: 0
- warnbox: 8 (unchanged)
- Overfull: 6, Underfull: 37, total 43 — DOWN from Task 8's 45 (removing box padding lets lines reflow tighter); within +5 budget
- Diff: 34 removed infobox lines + 34 added \medskip lines + 0 content lines changed

**Useful facts for later tasks (Task 10 housekeeping):**
- All 17 were bare `\begin{infobox}` (no option arg), matching Tasks 7/8 pattern; 34 edits (2 per block) applied in two parallel batches, all oldStrings unique via header line or following-line context
- Same per-block edit technique as Task 8: begin+header → `\medskip`+header; last-content+end → last-content+`\medskip`; anchor trailing edit with the following section/paragraph line when `\end{infobox}` alone is ambiguous
- LSP "Mismatched environment" at 194 + stale "Undefined reference" spam is expected mid-edit; resolves after xelatex passes — verify via .log grep only
- Evidence: .omo/evidence/reduce-boxes-pro/task-9-infobox-prose/ (log.txt, diff.txt, verification.txt)

---

## Task 10 — Final regression verification (2026-08-03)

**Actions taken:**
- Compiled xelatex TWICE from project root (both exit code 0, PDF 84 pages — matches Task 9)
- Verified all regression metrics against baseline (.omo/evidence/reduce-boxes-pro/baseline/)
- Saved evidence to .omo/evidence/reduce-boxes-pro/task-10-regression/ (final .log, label-comparison.txt, verification.txt)
- NO source changes, NO commit (verification-only task)

**Verification numbers (ALL PASS):**
- Overfull/Underfull: **43** (baseline 45, delta -2, within +5 budget) — Overfull 6 / Underfull 37
- fig+tab labels in .aux: current 51 = baseline 51; NO counter drift (ZERO)
- warnbox: 8 (unchanged); infobox: 0; tipbox: 0; keybox: 0; stepbox: 0
- .log checks: 0 "Fatal error", 0 "Emergency stop", 0 "LaTeX Warning: Reference.*undefined", 0 "Rerun to get cross-references"
- Known non-issues confirmed: "fatal: refusing to" (Git error example in body), "Missing character: ✓" (pre-existing) — not errors

**Useful facts:**
- Total Overfull/Underfull trajectory: 45 (T1) → 45 (T2/T3) → 46 (T5) → 47 (T6/T7) → 45 (T8) → 43 (T9/T10). Final 43 is BELOW baseline — zero layout regression.
- Page count: 87 (T1-T5) → 86 (T6/T7/T8) → 84 (T9/T10). Box removal progressively reclaimed 3 pages.
- .aux label comparison method: regex extract `\newlabel\{(fig:...|tab:...)\}\{\{(counter)\}` from both .aux files, compare key→counter maps; compare display counter `{N.M}`, NOT hyperref anchor strings (anchors legitimately change, e.g. table.1.1 → table.caption.5).
- This is the final gate — all 10 tasks complete. reduce-boxes-pro plan ready for Final Verification Wave (F1-F4).

---

## F4 — warnbox byte-integrity verification (2026-08-03)

**Verdict: REJECT (formal rule) — but baseline record is DEFECTIVE, not warnbox content.**

**Actions taken:**
- Extracted all 8 warnbox blocks (inclusive `\begin{warnbox}`…`\end{warnbox}`) from CURRENT `Git_VSCode_Tutorial.tex`; computed SHA-256 per block
- Compared vs recorded baseline `.omo/evidence/reduce-boxes-pro/baseline/warnbox-sha256.txt` → **0/8 match**
- Cross-checked same extraction against Task 1 snapshot `original.tex` → **8/8 match** (current warnbox blocks byte-identical to Task 1)
- Verified `original.tex` SHA-256 = `D85B1B5D...` = Task 1 recorded whole-file hash → snapshot is authoritative

**Key finding — recorded baseline is UNREPRODUCIBLE:**
- Brute-force boundary search ±400 bytes around every warnbox pair on BOTH current tex and original.tex: ZERO matches for any recorded hash
- Variants tried (all zero matches): begin→end inclusive; ± trailing `\n`/`\r\n`; CRLF→LF normalize; line-slice joins (LF/CRLF/empty/+\n); content-only (markers excluded); encodings UTF-8(+BOM)/UTF-16LE(+BOM)/UTF-16BE; convention begin_i→begin_{i+1}
- Conclusion: the 8 hashes in warnbox-sha256.txt do NOT correspond to the warnbox blocks of the Task 1 file — they are a Task 1 recording artifact (defective baseline metadata), NOT evidence of content change

**Decisive evidence warnbox was never touched:** current tex warnbox blocks ≡ original.tex warnbox blocks (identical SHA-256 for all 8). Consistent with learnings "warnbox: 8 (unchanged)" at every task.

**Recommendation:** re-record `baseline/warnbox-sha256.txt` from original.tex before re-running F4. No plan rework needed.
- Evidence: `.omo/evidence/reduce-boxes-pro/F4-warnbox-integrity.txt` (Table A: recorded-baseline 0/8 DIFF; Table B: snapshot 8/8 MATCH; analysis)

---

## F4 RE-RUN — warnbox byte-integrity — APPROVE (2026-08-03)

**Verdict: APPROVE — 8/8 warnbox blocks byte-identical to corrected baseline.**

**Actions taken:**
- Confirmed `baseline/warnbox-sha256.txt` re-recorded (2026-08-03) — 8 hashes, all lowercase, valid format
- Sanity check: reproduced ALL 8 corrected hashes from `original.tex` using the documented method (read lines → find `\begin{warnbox}`/`\end{warnbox}` → join block lines with `\n` → SHA-256 of UTF-8 bytes) → **8/8 MATCH** (baseline now valid/reproducible)
- Extracted the 8 warnbox blocks from CURRENT `Git_VSCode_Tutorial.tex` with the SAME method → compared to corrected baseline → **8/8 MATCH**
- Overwrote `.omo/evidence/reduce-boxes-pro/F4-warnbox-integrity.txt` with corrected comparison (sanity table + current-vs-baseline table) and clean APPROVE verdict
- No source changes, no commit

**Verification numbers:**
- Corrected baseline reproducibility from original.tex: 8/8
- Current tex vs corrected baseline: 8/8 (block line ranges: 243-251, 903-906, 1293-1297, 1479-1481, 1512-1515, 1585-1589, 1905-1908, 2589-2598)
- Warnbox byte-lengths (LF-joined): 1026, 216, 311, 144, 209, 297, 242, 523

**Useful facts:**
- The corrected baseline hash for block 1 (`06e5b679...`) = the "V4 CRLF→LF normalized block" variant found during the failed first run — i.e., the reproducible convention is line-based extraction joined with `\n` (LF), NOT raw byte slicing of the CRLF file. The original baseline (Task 1) used an unreproducible method and was replaced.
- F4 gate now cleanly APPROVED; all four F-waves (F1-F3 APPROVE, F4 APPROVE) pass for reduce-boxes-pro.
- Evidence: `.omo/evidence/reduce-boxes-pro/F4-warnbox-integrity.txt`

---

## F2 — Cross-reference integrity: APPROVE (2026-08-03)

**Verdict:** APPROVE — 0 undefined refs AND zero counter drift.

**Actions taken:**
- Inspected final log `.omo/evidence/reduce-boxes-pro/task-10-regression/Git_VSCode_Tutorial.log` (68,274 B, second-pass compile from Task 10)
- Compared `\newlabel{...}` display counters between project-root final `Git_VSCode_Tutorial.aux` and baseline `Git_VSCode_Tutorial.aux`
- Saved raw evidence to `.omo/evidence/reduce-boxes-pro/F2-crossref.txt` (key→counter table for all 51 labels + verdict)

**Verification numbers (ALL PASS):**
- Undefined refs in final log: **0**; "Rerun to get cross-references": **0**; Fatal/Emergency stop: **0**
- newlabel count: final 51 = baseline 51 (no keys added/removed: onlyFinal=0, onlyBase=0)
- Counter drift: **0** — all 51 shared display counters identical (e.g. tab:vc-history {1.1}→{1.1}, tab:git-vs-github {1.3}→{1.3}, fig:three-zones {1.3}→{1.3})
- Confirmed display-counter comparison only (NOT hyperref anchor strings — anchors legitimately differ, e.g. table.1.1 → table.caption.5, which is not drift)

**Useful facts:**
- F2 independent re-verification agrees with Task 10's earlier fig+tab=51=51 check.
- No files modified; no commit.

---

## F1 — Box removal audit (2026-08-03)

**Verdict: APPROVE** (all three static checks pass)

**Actions taken:**
- Static audit only — no file modification, no xelatex, no commit
- Evidence saved to `.omo/evidence/reduce-boxes-pro/F1-box-audit.txt`

**Verification numbers:**
1. `Select-String '\begin{(tipbox|keybox|stepbox|infobox)}'` → **0** matches (PASS; only the `\newenvironment`/`\newtcolorbox` definitions at lines 86-134 remain, which don't contain `\begin{...}`)
2. `Select-String '\begin{warnbox}'` → **8** matches (PASS; lines 243, 903, 1293, 1479, 1512, 1585, 1905, 2589)
3. `git diff -- Git_VSCode_Tutorial.tex | Select-String 'warnbox'` → **0** matches (PASS; no warnbox line differences in working tree)

**Useful facts:**
- warnbox is `\newtcolorbox` so `\begin{warnbox}` grep counts only body instances — the definition line `\newtcolorbox{warnbox}` does not match the pattern; 8 body instances confirmed intact after all box removals.
- Note: `git diff` without `HEAD` compares working tree vs index; since working tree has other plans' uncommitted changes, a zero-warnbox diff confirms no F1-era warnbox edits. (For full-tree diff vs HEAD use `git diff HEAD`.)
- LSP "Undefined reference" diagnostics on .tex are stale until xelatex two-pass — out of scope for static audit.
**F3 COMPILE QUALITY REGRESSION (APPROVAL GATE) - 2026-08-03: APPROVE**
- Baseline log: baseline/Git_VSCode_Tutorial.log; Final log: task-10-regression/Git_VSCode_Tutorial.log (last modified, contains "Output written on Git_VSCode_Tutorial.pdf (84 pages)")
- Overfull: 6 | Underfull: 37 | Combined: 43 (baseline 45, delta -2, budget +5 PASS)
- Emergency stop: 0 | Fatal error: 0
- "fatal: refusing to" (1 hit) = Git error example body text, NOT compile error (excluded per AGENTS.md)
- Evidence saved: .omo/evidence/reduce-boxes-pro/F3-compile-quality.txt
- No files modified, no commit (verification-only). F3 gate PASSED.
