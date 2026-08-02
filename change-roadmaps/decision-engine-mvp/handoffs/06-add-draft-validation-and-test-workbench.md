# CHG-06 — 实现 Draft 静态门禁、节点验证与整流测试

## 1. Change Goal

让策略师在生成版本前能够定位静态问题、执行到指定节点并运行当前 Draft 的完整路径，同时保证测试状态与生效环境隔离。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-05 已归档。

## 4. In Scope

- 画布问题面板和后端权威静态校验。
- 执行到节点。
- 整流测试工作台。
- NOT_REACHED、DEGRADED、FAILED 等结果状态。
- 测试输入和结果生命周期。
- List/Counter 测试命名空间。
- Draft 变更导致结果失效。

## 5. Out of Scope

- HTTP/Notify 的真实测试。
- Flow Version 和环境发布。
- 线上请求回放。

## 6. Normative Behavior Requirements

### TEST-001

- 静态校验必须覆盖拓扑、节点、表达式、资源、输出 Schema 和共享 Response 契约。

### TEST-002

- 前端即时提示不能替代后端权威门禁。

### TEST-003

- 执行到节点必须基于已持久化的明确 Draft revision。

### TEST-004

- 目标节点不在实际路径时必须返回 NOT_REACHED 和已执行路径。

### TEST-005

- 整流测试必须展示真实执行顺序、节点结果、错误、降级和状态记录摘要。

### TEST-006

- Draft 变化后旧节点验证和整流结果必须标记失效或清空。

### TEST-007

- 普通 Draft 测试不得修改 Dev/Test/Prod 生效名单和 Counter。

### TEST-008

- 测试结果默认只在当前会话保留，不作为线上历史记录。

## 7. Acceptance Outcomes

- 问题面板能定位所有阻断项并阻止后续版本生成。
- 节点验证成功、失败、降级和未到达均可复现。
- 整流测试输入可临时修改且不回写 Request 样例。
- 测试 Counter/List 状态不影响外部环境调用。
- Draft revision 变化后旧结果不再显示为有效。

## 8. Dependencies

- CHG-05

## 9. Handoff Guarantees to Later Changes

- 后续版本生成可以依赖统一权威门禁。
- 后续节点可以复用统一验证与测试执行模式。
- P0 在进入版本治理前具有可操作的配置质量闭环。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
