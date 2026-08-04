---
slug: chapter4-pr-conflicts
status: approved
intent: clear
review_required: false
pending-action: wait for user to run `$start-work`
approval-date: 2026-08-03
---

# Draft: chapter4-pr-conflicts

## Components (topology ledger)
<!-- id | outcome (one line) | status: active|deferred | evidence path -->
<!-- c1 | 分析 PR 冲突产生的原因 | active | 用户问题描述 + 第4章现有内容 -->
<!-- c2 | 制定 PR 冲突的解决步骤 | active | 用户问题描述 + Git 最佳实践 -->
<!-- c3 | 提供 PR 冲突的预防措施 | active | Git 工作流最佳实践 -->

## Open assumptions (announced defaults)
<!-- assumption | adopted default | rationale | reversible? -->
<!-- 位置 | 第4章"解决合并冲突"部分增加小节 | 用户明确要求写进第4章 | 否 -->
<!-- 深度 | 系统详细，包含原因、解决、预防 | 用户要求"系统详细" | 否 -->

## Findings (cited - path:lines)
- 第4章"解决合并冲突"部分（1819-1881行）已覆盖本地合并冲突的标记、VS Code 冲突解决体验、完整步骤
- 第5章"Pull Request（PR）"部分（2209-2333行）覆盖 PR 概念、标准流程、工作流程图，但缺少 PR 冲突内容
- 用户描述的场景：在分支上工作并发起 PR 后，继续在分支上推送新 commit，PR 提示冲突

## Decisions (with rationale)
- d1: 在第4章"解决合并冲突"部分增加"远程协作中的冲突（PR 冲突）"小节
- d2: 内容覆盖：原因分析、场景图解、解决步骤（命令行+VS Code）、预防措施

## Scope IN
- 在第4章增加"远程协作中的冲突（PR 冲突）"小节
- 包含原因分析、场景图解、解决步骤、预防措施

## Scope OUT (Must NOT have)
- 不修改第5章的 PR 内容（除非用户明确要求）
- 不改动第4章其他部分的内容

## Approval gate
status: approved (2026-08-03)
