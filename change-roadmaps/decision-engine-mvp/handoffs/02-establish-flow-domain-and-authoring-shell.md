# CHG-02 — 建立决策流领域模型与画布编辑骨架

## 1. Change Goal

建立项目分类、决策流、唯一 Draft、canonical graph 和桌面画布编辑骨架，使后续节点能力都能在同一 Draft 与拓扑模型上扩展。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-01 已归档。

## 4. In Scope

- 项目分类、决策流和唯一 Draft 的创建、读取、重命名、归类与删除语义。
- canonical graph 中的 node、port、edge、稳定 node id、namespaceKey 和布局元数据。
- 无环、无隐式 Merge、无并行的基础拓扑约束。
- Draft revision、自动保存、并发冲突和从服务端恢复。
- React Flow 画布、节点库壳、节点卡片壳、三栏编辑容器壳和问题面板壳。

## 5. Out of Scope

- 任何节点的完整业务配置和执行。
- 表达式、变量推断和输出 Schema。
- 版本、编译、测试、发布。

## 6. Normative Behavior Requirements

### FLOW-DOMAIN-001

- 每条决策流必须有且只有一个当前 Draft。

### FLOW-DOMAIN-002

- Draft 保存必须基于 revision；过期 revision 不得静默覆盖。

### FLOW-DOMAIN-003

- node id 与 namespaceKey 必须稳定，显示名变化不得破坏引用。

### FLOW-DOMAIN-004

- MVP 图模型必须拒绝循环、隐式 Merge、并行和普通节点多入多出。

### FLOW-DOMAIN-005

- 项目只用于分类，不进入权限、环境、Key 或运行隔离。

### FLOW-DOMAIN-006

- 删除项目必须把决策流移动到未归类，不得级联删除决策流。

### FLOW-DOMAIN-007

- 前端布局状态与领域配置必须可区分，布局变化不得改变执行语义。

## 7. Acceptance Outcomes

- 管理员可以创建项目和决策流，打开空 Draft 并在画布中增删通用节点壳。
- 并发编辑使用旧 revision 保存时得到明确冲突。
- 非法拓扑被前后端一致拒绝。
- 节点改名后稳定标识不变化。
- 删除项目后决策流仍存在并进入未归类。

## 8. Dependencies

- CHG-01

## 9. Handoff Guarantees to Later Changes

- 后续 Change 可以注册具体节点类型而不重新定义 graph。
- 后续 Change 可以基于稳定 node id、namespaceKey 和 Draft revision 建立引用、版本和 Diff。
- 画布和三栏容器已具备承载节点专属 UI 的扩展点。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
