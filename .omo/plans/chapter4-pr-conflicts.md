# Plan: chapter4-pr-conflicts

## Objective
在第4章"分支与合并"中系统、详细地增加"Pull Request 冲突"相关内容，解答用户关于"在分支上继续工作后 PR 提示冲突"的疑问。

## Context
- **第4章现状**：已有"解决合并冲突"小节（1819-1881行），覆盖本地合并冲突的标记、VS Code 冲突解决体验、完整步骤。但缺少**远程协作场景**（PR 冲突）的内容。
- **第5章现状**："Pull Request（PR）"小节（2209-2333行）覆盖 PR 概念、标准流程、工作流程图，但**缺少 PR 冲突**的内容。
- **用户场景**：在 feature 分支上工作并发起 PR 后，继续在分支上 push 新 commit，PR 提示冲突。

## Decision
在第4章"解决合并冲突"部分（1819行之后）增加一个子节 **"远程协作中的冲突（PR 冲突）"**，专门讲解 PR 冲突的产生原因、解决方法和预防措施。

理由：
- 用户明确要求写进第4章
- 第4章已有"解决合并冲突"的内容，PR 冲突是其自然延伸
- 保持第5章 PR 流程内容的完整性，避免重复

## Scope IN
- 在第4章"解决合并冲突"部分增加"远程协作中的冲突（PR 冲突）"子节
- 内容覆盖：
  1. **PR 冲突的产生原因**（文字描述 + ASCII 时间线图）
  2. **PR 冲突与本地冲突的区别**
  3. **解决 PR 冲突的方法**（命令行：fetch/rebase/merge + VS Code 图形界面操作）
  4. **预防措施**（经常同步 main、小步快跑、PR 合并前同步）

## Scope OUT (Must NOT have)
- 不修改第5章的 PR 内容（避免重复）
- 不改动第4章其他部分的内容
- 不新增图片（复用现有的 merge-conflict.png 等图片）

## Todos

- [x] 1. 在第4章"解决合并冲突"部分增加"远程协作中的冲突（PR 冲突）"子节
  - **Where**: `Git_VSCode_Tutorial.tex`，1819行之后（`\section{解决合并冲突}`之后）
  - **What**: 增加 `\subsection{远程协作中的冲突（PR 冲突）}` 及其内容
  - **Content**:
    - 原因分析：main 分支在 PR 创建后有了新的 commit，导致分支与 main 产生冲突
    - 场景图解：用 ASCII 时间线展示冲突产生的典型场景
    - 与本地冲突的区别：PR 冲突发生在远程，本地合并冲突发生在本地
    - 解决方法：
      - 命令行：`git fetch origin` + `git rebase origin/main` 或 `git merge origin/main`
      - VS Code：同步按钮 + 冲突解决
    - 预防措施：经常同步 main、小步快跑、PR 合并前同步
  - **Acceptance**: 
    - 新增子节包含上述5个部分内容
    - 文字描述清晰、准确
    - 命令经过验证（可执行）
    - 与第4章现有风格一致
  - **QA**: 
    - 编译验证：`xelatex Git_VSCode_Tutorial.tex` 无错误
    - 内容验证：与用户描述的场景一致
    - 交叉引用验证：无 broken reference

## Final verification wave

- [x] F1. `xelatex Git_VSCode_Tutorial.tex` 编译通过，无 fatal error
- [x] F2. 新增子节内容完整，覆盖原因、区别、解决方法、预防措施
- [x] F3. 新增内容风格与第4章现有内容一致（使用 \lstlisting、\tipbox、\warnbox 等）
- [x] F4. 无 broken reference 或 missing image
- [x] F5. 命令经过验证，可执行

## Notes
- 第4章"解决合并冲突"部分已有冲突标记、VS Code 冲突解决体验、完整步骤的内容，新增子节应与其衔接自然
- 第5章"Pull Request（PR）"部分已有 PR 概念和流程的内容，新增子节应避免重复
- 用户使用中文交流，计划使用中文撰写
