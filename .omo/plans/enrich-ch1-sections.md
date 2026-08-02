# enrich-ch1-sections

- **slug**: `enrich-ch1-sections`
- **created**: 2026-08-02
- **status**: approved
- **intent**: CLEAR
- **review_required**: false
- **source**: C:\Users\Admin\Desktop\Git\Git_VSCode_Tutorial.tex

## Scope

Expand **Section 1.2** (Git 简介, ~25 → ~65+ lines) and deepen **Section 1.3** (Git 内部数据模型, ~94 → ~130+ lines). Section 1.1 is unchanged.

## Out of scope / Must NOT

- Must NOT add new chapter sections (7 章固定结构)
- Must NOT modify `sc-*.md` 参考文档
- Must NOT change existing writing style
- Must NOT introduce new tcolorbox environments
- Must NOT add new images (no screenshots needed)
- Must NOT touch sections outside 1.2 and 1.3

## Decision log

- **D1**: Section 1.1 unchanged — adequate at ~54 lines
- **D2**: 1.2 expansion uses existing keybox + inline paragraphs; no new subsections
- **D3**: 1.3 additions insert under existing 1.3 headings; new sub-subsections within 1.3 are permitted
- **D4**: All new content follows existing conventions: 中文正文 + English term on first use, tcolorbox usage, lslisting shell style

## Todos

### Phase 1: Expand Section 1.2

- [x] 1. Read section 1.2 current content with surrounding context
  - **Action**: Read `Git_VSCode_Tutorial.tex` lines 430-460 (section 1.2 + boundary context)
  - **Acceptance**: Map exact insertion points: (a) after "Linus 自嘲式的幽默", (b) within keybox after each \item, (c) after keybox before section 1.3
  - **QA**: Verify line number ranges are accurate by spot-checking content match

- [x] 2. Insert BitKeeper backstory + distributed-vs-centralized deep comparison
  - **WHERE**: After "Linus 自嘲式的幽默。" (current line ~433), before "**为什么 Git 能胜出？**"
  - **HOW**: Write 2-3 paragraph block: (a) 2002 Linux kernel adopted BitKeeper as first VCS; 2005 license revoked → Linus wrote Git in 4 days as self-hosted replacement, (b) Infobox with comparison table: 工作方式 | 离线能力 | 分支成本 | 数据完整性 | 代表工具 for centralized vs distributed
  - **EXPECT**: Section 1.2 gains ~15 lines of backstory + comparison, total ~40 lines
  - **QA**: Compile → verify paragraph appears between origin story and "为什么能胜出" section

- [x] 3. Insert adoption statistics + ecosystem context
  - **WHERE**: After "几乎所有开源项目和绝大多数商业软件团队都在使用它。" (current line ~439)
  - **HOW**: Write 1 paragraph: Stack Overflow 2024 Developer Survey — Git 93%+ adoption among professional developers; GitHub 1亿+ 开发者; GitLab/Bitbucket/Gitee as alternatives; Linux kernel is the largest Git repo (引用数据要准确)
  - **EXPECT**: 1 compact paragraph, ~4-5 lines
  - **QA**: Verify paragraph flows naturally into next section (1.2 核心特性)

- [x] 4. Expand 5 core features in keybox with "so what" explanation
  - **WHERE**: Edit the existing keybox (lines 443-455) — keep the 5 \item structure but add a second sentence to each explaining the practical consequence
  - **HOW**: Each item gets one extra sentence:
    - 分布式架构 → "这意味着即使 GitHub 服务器宕机，你依然可以提交、查看历史、切换分支。"
    - 快照式存储 → "因此 checkout 任意历史版本是 O(1) 操作，不受项目规模影响。"
    - 强大的分支 → "创建一个分支仅占用 41 字节，这是 Git 鼓励频繁使用分支的根本原因。"
    - 数据完整性 → "SHA-1 哈希链保证任何历史篡改（哪怕是单字节）都会被立即检测。"
    - 暂存区设计 → "这让你可以将一个大改动拆分为多个语义清晰的提交，而非一个混乱的大提交。"
  - **EXPECT**: Keybox items grow from 1 sentence to 2 sentences each
  - **QA**: Compile → verify keybox renders all 5 items without overflow

- [x] 5. Add transition paragraph to section 1.3
  - **WHERE**: After the keybox, before `\section{Git 的内部数据模型}` (current line ~456)
  - **HOW**: Write 2-3 sentence transition: "理解了 Git 解决了什么问题以及它的设计哲学之后，让我们深入底层，看看 Git 是如何存储和寻址数据的。这一节的内容是理解 Git 命令背后原理的关键——你不必背诵，但理解后会让你在遇到问题时不再困惑。"
  - **EXPECT**: Smooth transition paragraph, ~3 lines
  - **QA**: Verify paragraph appears between 1.2 and 1.3, no LaTeX errors

### Phase 2: Deepen Section 1.3

- [x] 6. Read section 1.3 current content with surrounding context
  - **Action**: Read `Git_VSCode_Tutorial.tex` lines 457-560 (section 1.3 + boundary)
  - **Acceptance**: Map exact insertion points for 6 deepening items
  - **QA**: Verify line mappings by cross-referencing content

- [x] 7. Add Tag object (5th object type)
  - **WHERE**: After the four-object diagram + itemize list (lines 483-488), before "一次提交的完整结构"
  - **HOW**: Add a new itemize block or extend existing one:
    - **Tag（标签）**：指向特定 Commit 的固定引用，通常用于标记版本号（v1.0, v2.3）。
    - Annotated Tag：存储完整对象（包含标签名、日期、注释、PGP 签名），创建命令 `git tag -a v1.0 -m "Release 1.0"`
    - Lightweight Tag：只是指向 Commit 的指针（等价于不会移动的 Branch），创建命令 `git tag v1.0`
  - Also update the 4-object diagram text if needed (keep diagram itself, just add text below)
  - **EXPECT**: ~8-10 lines added, introducing Tag as 5th object type
  - **QA**: Compile → verify Tag explanation appears after the four-object list

- [x] 8. Add HEAD and refs mechanism
  - **WHERE**: After the "Branch 只是一个指向 Commit 的指针" explanation (~line 487), or as a new sub-subsection
  - **HOW**: New sub-subsection `\subsection{HEAD 与引用}`:
    - HEAD 指向当前检出的分支（`.git/HEAD` 文件内容：`ref: refs/heads/main`）
    - Detached HEAD 状态（HEAD 直接指向 Commit 哈希）
    - `.git/refs/heads/` 存放本地分支指针，`.git/refs/tags/` 存放标签
    - 可视化：`git log --oneline --all --graph` 展示分支与 HEAD 关系
  - **EXPECT**: ~12-15 lines, a new \subsection under 1.3
  - **QA**: Verify new subsection appears in TOC after recompilation

- [x] 9. Add content addressing proof with git hash-object
  - **WHERE**: After "Git 中的一切都是哈希" keybox (~line 492), before "一次提交的完整结构"
  - **HOW**: New infobox:
    ```
    \begin{infobox}
    \textbf{验证内容寻址}：
    用底层命令验证：同一内容无论文件名为何，Blob 哈希相同。
    \begin{lstlisting}[style=shell]
    echo "Hello Git" | git hash-object --stdin
    # 输出固定哈希值（例如：8ab686e...）
    echo "Hello Git" | git hash-object --stdin  
    # 再次运行，输出相同哈希
    \end{lstlisting}
    这就是内容寻址的核心：Git 通过内容计算哈希，而非通过文件名定位。
    \end{infobox}
    ```
  - **EXPECT**: ~10 lines infobox with concrete demo
  - **QA**: Verify the shell code renders correctly with listings style

- [x] 10. Add plumbing command demo
  - **WHERE**: After "一次提交的完整结构" diagram, before "为什么 Git 高效" infobox
  - **HOW**: New infobox:
    ```
    \begin{infobox}
    \textbf{用底层命令查看对象}：
    Git 提供了底层命令来直接查看四种对象的内容：
    \begin{lstlisting}[style=shell]
    git cat-file -p HEAD          # 查看 HEAD 指向的 Commit 对象
    git cat-file -p HEAD^{tree}   # 查看该 Commit 对应的 Tree 对象
    git ls-tree HEAD              # 列出 Tree 中的文件与 Blob 映射
    git cat-file -p <blob-hash>   # 查看 Blob 的内容
    \end{lstlisting}
    \end{infobox}
    ```
  - **EXPECT**: ~12 lines infobox connecting data model to real commands
  - **QA**: Verify shell listing renders without overflow

- [x] 11. Add GC and packfile to "为什么 Git 高效" infobox
  - **WHERE**: Edit the existing infobox (lines 543-550) — add 1 more item
  - **HOW**: Add a 4th item:
    - \item \textbf{自动垃圾回收}：Git 定期运行 \texttt{git gc}，将松散对象（loose objects）打包为 packfile 和索引文件，通过 delta 压缩进一步节省存储空间。
  - **EXPECT**: Infobox grows from 3 items to 4 items
  - **QA**: Verify infobox renders all 4 items

- [x] 12. Add Porcelain vs Plumbing tipbox
  - **WHERE**: At the end of section 1.3, before the chapter 1 summary tipbox (line ~640 area -- actually check exact position)
  - **HOW**: New tipbox:
    ```
    \begin{tipbox}
    \textbf{Porcelain vs Plumbing}：
    Git 命令分为两层——\textbf{表层（Porcelain）}是你日常使用的命令（git add, git commit, git log），
    设计为人类友好；\textbf{底层（Plumbing）}是 Git 内部使用的命令（git cat-file, git hash-object, git ls-tree），
    输出格式稳定，适合脚本调用。理解数据模型时，底层命令是最好的"显微镜"。
    日常工作中你几乎不需要直接使用 Plumbing 命令，但了解它们能帮你深入诊断问题。
    \end{tipbox}
    ```
  - **EXPECT**: ~8 lines tipbox, concisely explaining the two-layer design
  - **QA**: Verify tipbox appears correctly at end of section 1.3

### Phase 3: Compile and Verify

- [x] 13. Compile xelatex twice
  - **Action**:
    ```
    C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex
    C:\Users\Admin\AppData\Local\Programs\MiKTeX\miktex\bin\x64\xelatex.exe Git_VSCode_Tutorial.tex
    ```
  - **Acceptance**: No fatal errors; Overfull/Underfull warnings are acceptable per AGENTS.md
  - **QA**: Check exit code is 0 for both runs; verify TOC page numbers are correct

- [x] 14. Verify output PDF
  - **Action**: Use `look_at` or `read` to inspect the PDF for correct rendering of new content
  - **Acceptance**:
    - Section 1.2 now spans ~65+ lines (visible in source)
    - Section 1.3 now spans ~130+ lines (visible in source)
    - All new tcolorbox environments render correctly
    - Shell listings render with proper formatting
    - No missing references or broken cross-references
    - 章节目录 (TOC) reflects any new subsection (HEAD 与引用)
  - **QA**: PDF opens without errors; visual inspection of key pages

## Final verification wave

- [x] F1. Source structure audit: Count lines per section, verify 1.2 ≥ 60 lines and 1.3 ≥ 125 lines
- [x] F2. Content audit: Verify all 5 expansion points in 1.2 and all 6 deepening points in 1.3 exist
- [x] F3. Style audit: Confirm no emoji, Chinese body + English terms, correct tcolorbox usage
- [x] F4. Compile audit: Confirm 2-pass xelatex produces PDF with correct TOC
