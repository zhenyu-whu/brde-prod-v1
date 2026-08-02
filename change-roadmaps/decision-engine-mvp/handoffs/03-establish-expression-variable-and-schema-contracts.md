# CHG-03 — 建立值、表达式、变量与输出 Schema 契约

## 1. Change Goal

建立跨前端、控制面和 Go 执行面的统一值类型、稳定变量引用、表达式子集、错误语义和输出 Schema 生命周期。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-01、CHG-02 已归档。
- 表达式 Spike 已完成或提供可执行结论。

## 4. In Scope

- Fixed/Expression 通用值模型和编辑器基础。
- 稳定变量 token 的存储、展示、复制和失效语义。
- 明确 CEL 子集、基础类型、null/missing、函数和错误分类。
- TypeScript 编辑期校验与 Go 权威运行时。
- 共享 golden fixtures。
- 静态、声明和推断输出 Schema 的统一状态模型。

## 5. Out of Scope

- 具体业务节点表单。
- Function JavaScript 运行时。
- HTTP 或其他节点的具体动态 Schema 推断流程。

## 6. Normative Behavior Requirements

### EXPR-001

- Go 求值结果必须是运行时权威语义，前端不得实现另一套不同解释器。

### EXPR-002

- Fixed 值必须保留声明类型，不进行不可见的字符串自动转换。

### EXPR-003

- 变量引用必须绑定 namespaceKey 与字段路径，节点改名不得破坏引用。

### EXPR-004

- missing 和显式 null 的基础取值都返回 null，但不合法运算必须产生类型错误。

### EXPR-005

- 表达式必须具有稳定的语法、类型、引用和运行错误分类。

### EXPR-006

- TypeScript 与 Go 必须运行同一组 golden fixtures。

### EXPR-007

- 输出 Schema 必须具有 MISSING、VALID、STALE、CONFLICTED 等显式状态，并可持久化。

### EXPR-008

- 下游引用不得依赖只存在于当前浏览器会话的临时推断。

## 7. Acceptance Outcomes

- 同一 fixture 在 TypeScript 校验和 Go 求值中得到一致结果或一致错误分类。
- 节点改名后表达式仍能求值。
- 无效引用和类型错误在编辑期可见，在运行期稳定失败。
- 输出 Schema 状态可以随 Draft 保存和恢复。
- 固定值与表达式值使用统一序列化结构。

## 8. Dependencies

- CHG-01
- CHG-02

## 9. Handoff Guarantees to Later Changes

- 所有后续节点可以共享 Value Editor、变量 token、类型和错误模型。
- Decision Table、Condition、Response、Counter 等 Change 无需各自定义表达式。
- 动态输出节点可以复用统一 Schema 生命周期。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
