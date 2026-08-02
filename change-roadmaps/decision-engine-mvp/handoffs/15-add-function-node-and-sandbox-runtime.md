# CHG-15 — 实现 Function 节点与隔离运行时

## 1. Change Goal

提供受控复杂数据转换能力，同时保证多租户实时执行的资源、安全和可解释边界。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-03、CHG-06、CHG-07、CHG-10 已归档。
- Function 隔离 Spike 已选择可行方案。

## 4. In Scope

- Function 输入映射、代码编辑、静态检查和验证结果。
- 受控模块形态和白名单模块。
- 超时、内存、输出大小和并发限制。
- 禁止宿主 IO。
- 输出 Schema 快照和失效。
- 源码、依赖和输出 Schema Diff。
- 高风险权限和审计。

## 5. Out of Scope

- 任意 npm 包。
- 网络、文件、环境变量和系统调用。
- 通用任务调度或长任务。
- 把 Function 作为 HTTP/Notify 替代。

## 6. Normative Behavior Requirements

### FUNCTION-001

- 只有具备 Function 编辑能力的用户可以修改代码。

### FUNCTION-002

- 运行时必须禁止任意宿主 IO 和动态未授权模块。

### FUNCTION-003

- 必须强制超时、内存、输出大小和并发限制。

### FUNCTION-004

- 根返回值必须为 object。

### FUNCTION-005

- 代码或输入变化后旧输出 Schema 必须 STALE。

### FUNCTION-006

- 存在下游引用时，STALE/MISSING/CONFLICTED Schema 不得生成版本。

### FUNCTION-007

- 错误必须返回类型、消息、源码位置和受控堆栈。

### FUNCTION-008

- 运行时故障不得拖垮 executor 其他租户请求。

## 7. Acceptance Outcomes

- 正常转换、语法错误、无限循环、超大输出和内存攻击均有确定结果。
- 禁止的网络和宿主对象不可访问。
- 输出 Schema 失效会阻止版本生成。
- 权限不足用户只能只读。
- Function 资源耗尽不影响其他租户基准流量。
- Diff 和审计包含依赖、源码和输出 Schema。

## 8. Dependencies

- CHG-03
- CHG-06
- CHG-07
- CHG-10

## 9. Handoff Guarantees to Later Changes

- 11 类节点全部完成。
- 复杂转换不需要扩张表达式或开放外部 IO。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
