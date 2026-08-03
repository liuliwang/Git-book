# Learnings — chapter4-pr-conflicts

Conventions, patterns, and successful approaches discovered during work on this plan.

_Auto-scaffolded by /start-work. Append new entries below - never overwrite._

---

## 编译验证结果（任务1）
- xelatex 编译通过两次，无 fatal error
- .log 中无 undefined reference 警告
- 新增子节位置正确（1881 行「解决合并冲突」之后、\section{Rebase：整理历史} 之前）
- 复用宏：tipbox, warnbox, infobox, keybox, lstlisting[style=shell], verbatim
- 注意：verbatim 等宽字体中使用 Unicode 特殊字符可能导致 Missing character 警告，建议用 ASCII 字符
- 注意：PowerShell Add-Content 默认编码会写坏 UTF-8 文件，追加时需用 [System.IO.File]::AppendAllText(..., UTF8)