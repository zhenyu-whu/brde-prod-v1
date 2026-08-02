# CHG-09 — 增加 Derived、Condition 与多返回响应

## 1. Change Goal

在既有单路径图模型上增加可命名中间计算、显式互斥分支和 Response 多返回结构，同时保持无汇合和单路径执行。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-08 已归档，P0 里程碑已完成。

## 4. In Scope

- Derived Variables 节点。
- Condition 有序分支、条件组和固定兜底。
- 多 Response 分支。
- Response 从单一结构显式升级为公共字段加多个返回结构。
- 分支路径变量可见性、NOT_REACHED 和可解释结果。
- 图校验、Diff、编译和测试适配。

## 5. Out of Scope

- Merge、并行、循环。
- 决策表高级冲突分析。
- 灰度和 A/B。

## 6. Normative Behavior Requirements

### BRANCH-001

- Derived 同节点变量只能引用上游，不能相互依赖。

### BRANCH-002

- Condition 必须有固定兜底分支，普通分支按顺序首个命中。

### BRANCH-003

- 每个分支必须最终到达独立 Response，不得重新汇合。

### BRANCH-004

- 下游变量面板只能展示当前节点前序可达变量。

### BRANCH-005

- 未进入的分支节点必须标记 NOT_REACHED。

### BRANCH-006

- 分支排序变化必须作为语义 Diff。

### BRANCH-007

- Response 多返回模式必须由用户显式升级，并保留公共字段与具名结构。

### BRANCH-008

- 每个 Response 必须绑定一个返回结构并完整映射公共字段和该结构必填字段。

## 7. Acceptance Outcomes

- 同一输入只执行一个 Condition 分支。
- 兜底分支在普通分支均未命中时执行。
- 非法汇合被拒绝。
- Derived 输出可被后续节点引用。
- 节点验证和整流测试正确展示已执行与未到达分支。
- 单一 Response 契约可以显式升级为多返回模式，已有映射通过迁移步骤保留。
- 不同 Response 可以绑定不同结构并返回各自合法响应。

## 8. Dependencies

- CHG-08

## 9. Handoff Guarantees to Later Changes

- 后续 HTTP、Notify、Function 可以参与分支但无需重新定义路由。
- 完整 MVP 具备基础流程决策表达力。
- Response 多返回结构不再留给 Prod 治理 Change 临时补充。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
