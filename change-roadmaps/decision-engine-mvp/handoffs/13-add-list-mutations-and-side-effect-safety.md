# CHG-13 — 补齐名单写操作与统一副作用安全

## 1. Change Goal

在既有 List 资源和测试模式上增加 add/remove/refresh_ttl，并建立对状态副作用的一致权限、幂等、确认和审计行为。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-05、CHG-06、CHG-10 已归档。

## 4. In Scope

- List add/remove/refresh_ttl。
- 默认 TTL、执行前后状态、规范化和幂等。
- Draft 测试命名空间。
- 控制台环境名单单 key 维护。
- 副作用摘要、权限要求和审计。

## 5. Out of Scope

- 批量导入、批量清理和大规模运营。
- 跨环境共享名单。
- HTTP/Notify 外部副作用。

## 6. Normative Behavior Requirements

### LIST-WRITE-001

- 写操作必须作用于明确环境和名单，不得默认全环境共享。

### LIST-WRITE-002

- add 和 refresh_ttl 使用资源默认 TTL。

### LIST-WRITE-003

- 同一幂等请求重试不得重复产生不期望状态变化。

### LIST-WRITE-004

- 普通 Draft 测试只能写测试命名空间。

### LIST-WRITE-005

- 真实环境写入要求名单数据维护能力和审计。

### LIST-WRITE-006

- 节点结果必须展示规范化 key、执行前状态、应用结果和执行后状态。

### LIST-WRITE-007

- 空 key 或类型不兼容必须稳定失败，不得写入。

## 7. Acceptance Outcomes

- 四种 List 操作具有统一输出结构。
- Draft 测试写入不影响外部 Dev/Test/Prod。
- 真实环境写入受权限控制并可审计。
- TTL 刷新和删除结果可验证。
- 重复请求不会产生异常重复副作用。

## 8. Dependencies

- CHG-05
- CHG-06
- CHG-10

## 9. Handoff Guarantees to Later Changes

- 名单能力达到完整 MVP 范围。
- 统一副作用设计可供后续高风险操作复用。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
