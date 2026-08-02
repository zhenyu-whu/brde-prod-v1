# CHG-16 — 完成审计、执行日志、指标、可靠性与 MVP 硬化

## 1. Change Goal

将各阶段已建立的最小审计、日志和指标扩展为可运营、可恢复并通过完整 MVP 准入测试的系统。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- P0 和 P1 功能 Change 已按依赖完成。

## 4. In Scope

- 审计日志查询和详情。
- 最小审计事务与异步富化完整链路。
- 执行日志 durable path、ClickHouse 表达和补偿。
- OpenTelemetry、Prometheus/Grafana 内部仪表盘和告警。
- 限流、配额、缓存击穿和降级。
- 备份恢复、故障注入、性能、安全和完整 E2E。
- 文档、运维手册和 MVP 发布准入。

## 5. Out of Scope

- 客户可见实时监控。
- 请求回放。
- 策略效果归因。
- Post-MVP 功能。

## 6. Normative Behavior Requirements

### HARDEN-001

- 最小审计事件必须与业务事务一致，异步富化失败不能丢失基础记录。

### HARDEN-002

- 执行日志写出不得阻塞业务，并必须有可持久化补偿路径。

### HARDEN-003

- 日志链路至少一次投递必须可去重或容忍重复。

### HARDEN-004

- 指标不得使用不受控高基数业务字段。

### HARDEN-005

- 控制面故障时已缓存 Artifact 必须继续执行。

### HARDEN-006

- 缓存击穿、Redis/ClickHouse/队列故障必须有明确降级和告警。

### HARDEN-007

- 性能、租户隔离、SSRF、Key 撤销、Function 资源和发布并发测试必须通过。

### HARDEN-008

- 备份恢复必须验证 PRD RPO/RTO 目标。

### HARDEN-009

- 完整 MVP 所有 Requirement Group 必须在覆盖矩阵和自动化测试中闭环。

## 7. Acceptance Outcomes

- 用户可以按时间、操作者、对象、结果和环境查询审计。
- ClickHouse 不可用时决策继续，并在恢复后补偿日志。
- 内部仪表盘展示执行、发布、队列和依赖健康。
- 控制面停止后已有流仍能执行。
- 性能、安全、灾备和故障测试达到准入标准。
- 覆盖矩阵不存在未解释缺口。

## 8. Dependencies

- CHG-09
- CHG-12
- CHG-13
- CHG-14
- CHG-15

## 9. Handoff Guarantees to Later Changes

- 完整 MVP 达到工程发布准入。
- 后续 Change 可以从稳定 specs、代码和运维基线继续演化。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
