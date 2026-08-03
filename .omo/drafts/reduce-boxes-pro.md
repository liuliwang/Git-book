# reduce-boxes-pro — 草稿

## 意图路由
- **intent**: clear
- **review_required**: false
- **分类**: Architecture

## 状态
status: plan-ready

## 计划文件
`.omo/plans/reduce-boxes-pro.md`

## 决策记录
- 减少策略: 激进（仅保留 warnbox ~8 个）
- 表格: 提取为独立 table 环境
- 学习目标: 转为粗体段落

## Metis 间隙发现（已折叠到计划）
- 5 类边缘情况 + 编译风险（代码块/表格/嵌套/颜色/标签）
- 7 项缺失约束（目录页码/lstlisting 样式迁移/转换顺序/检查点/Overfull 预算/无新包/标签命名空间）
- 5 项范围爬升危险（改写 prose/样式清理/表格重排版/替代分隔符/图片重定位）
- 10 项验收标准（已嵌入各任务 QA + 最终验证波 F1-F4）

## 下一步
用户运行 `$start-work reduce-boxes-pro` 执行计划。
