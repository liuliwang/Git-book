# simplify-boxes

- **slug**: `simplify-boxes`
- **created**: 2026-08-02
- **status**: approved
- **intent**: CLEAR
- **review_required**: false
- **source**: C:\Users\Admin\Desktop\Git\Git_VSCode_Tutorial.tex

## Scope

Replace the 5 colored tcolorbox environments with differentiated monochrome alternatives, reducing visual noise while preserving semantic hierarchy. Only preamble definitions change (lines 86-132); all 97 body instances are untouched.

| Environment | Old | New |
|------------|-----|-----|
| `warnbox` | Red box | Thin black frame, white bg, red icon retained |
| `keybox` | Orange box | Left bar only (3pt black rule, no frame) |
| `stepbox` | Purple box | Bold heading + enumerate (NO box) |
| `tipbox` | Green box | Bold heading + paragraph (NO box) |
| `infobox` | Blue box | Thin gray frame, white bg |

## Out of scope / Must NOT

- Must NOT edit body content (97 instances untouched)
- Must NOT remove or change FontAwesome icons
- Must NOT add new LaTeX packages
- Must NOT change document structure

## Todos

### Phase 1: Redefine Environments

- [x] 1. Redefine warnbox to monochrome framed box
  - **WHERE**: `Git_VSCode_Tutorial.tex` lines 98-105
  - **HOW**: Replace with:
    ```latex
    \newtcolorbox{warnbox}[1][]{
      colback=white,
      colframe=black!40,
      fonttitle=\bfseries,
      title={\faExclamationTriangle\; 注意},
      breakable,
      #1
    }
    ```
  - **EXPECT**: Black 0.4pt frame, white background, red triangle icon stays

- [x] 2. Redefine keybox to left-bar style
  - **WHERE**: `Git_VSCode_Tutorial.tex` lines 116-123
  - **HOW**: Replace with:
    ```latex
    \newtcolorbox{keybox}[1][]{
      colback=white,
      colframe=black!30,
      leftrule=3pt,
      rightrule=0pt,
      toprule=0pt,
      bottomrule=0pt,
      arc=0pt,
      fonttitle=\bfseries,
      title={\faKey\; 核心概念},
      breakable,
      #1
    }
    ```
  - **EXPECT**: Only left 3pt bar visible, no box

- [x] 3. Redefine stepbox to bold heading (no box)
  - **WHERE**: `Git_VSCode_Tutorial.tex` lines 125-132
  - **HOW**: Replace `\newtcolorbox{stepbox}` with a plain `\newenvironment`:
    ```latex
    \newenvironment{stepbox}{%
      \medskip
      \noindent\textbf{\faCogs\; 操作步骤}
      \smallskip
    }{%
      \medskip
    }
    ```
  - **EXPECT**: "⚙ 操作步骤" heading, no colored box, enumerate body flows naturally

- [x] 4. Redefine tipbox to bold heading (no box)
  - **WHERE**: `Git_VSCode_Tutorial.tex` lines 89-96
  - **HOW**: Replace `\newtcolorbox{tipbox}` with:
    ```latex
    \newenvironment{tipbox}{%
      \medskip
      \noindent\textbf{\faLightbulb\; 提示}
      \smallskip
    }{%
      \medskip
    }
    ```
  - **EXPECT**: "💡 提示" heading, no colored box

- [x] 5. Redefine infobox to monochrome thin frame
  - **WHERE**: `Git_VSCode_Tutorial.tex` lines 107-114
  - **HOW**: Replace with:
    ```latex
    \newtcolorbox{infobox}[1][]{
      colback=white,
      colframe=black!25,
      fonttitle=\bfseries,
      title={\faInfoCircle\; 说明},
      breakable,
      #1
    }
    ```
  - **EXPECT**: Very light gray frame (0.25 black opacity), white bg

### Phase 2: Compile and Verify

- [x] 6. Compile xelatex twice
  - **Action**:
    ```
    C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex
    C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex
    ```
  - **Acceptance**: No fatal errors; Overfull/Underfull warnings acceptable
  - **QA**: Check exit code 0 for both runs

- [x] 7. Verify output PDF
  - **Action**: Inspect PDF for correct rendering of all 5 new styles
  - **Acceptance**:
    - warnbox: thin black frame visible, white background
    - keybox: left bar only, no colored background
    - stepbox: bold heading only, no box, enumerate rendered
    - tipbox: bold heading only, no box, paragraph text follows
    - infobox: very light gray frame, white background
    - All FontAwesome icons present
    - No page breaks or layout regressions
  - **QA**: Visual inspection of key pages (chapters 1-2 cover all 5 types)

## Final verification wave

- [x] F1. Definition audit: Confirm all 5 environments are redefined (no old colored definitions remain)
- [x] F2. Body audit: Confirm 97 body instances untouched, no stray color references
- [x] F3. Visual audit: Confirm no colored boxes (only black/gray frames + icons)
- [x] F4. Compile audit: Confirm 2-pass xelatex with zero fatal errors

