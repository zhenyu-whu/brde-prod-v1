# CHG-12 — 实现 HTTP 与 Notify 外联节点

## 1. Change Goal

在连接器和测试安全模型上实现外部 HTTP 查询与通知提交，并保持可解释、受限和可审计。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-03、CHG-06、CHG-11 已归档。

## 4. In Scope

- HTTP 节点请求配置、环境解析、响应外壳和动态 body Schema。
- Notify 通道类型、目标、模板内容和提交结果。
- Draft 受保护测试和显式真实测试。
- 超时、重试、幂等、响应大小和 egress 安全。
- 执行日志、Diff、资源指纹和凭据版本摘要。

## 5. Out of Scope

- 最终送达回执。
- HTML 邮件、卡片消息、自定义任意 Header。
- 用户任意完整 URL。
- 通用异步工作流。

## 6. Normative Behavior Requirements

### EXTERNAL-NODE-001

- HTTP 必须选择连接器并只配置相对路径和允许参数。

### EXTERNAL-NODE-002

- HTTP 输出为 ok/statusCode/body，诊断信息默认不进入变量空间。

### EXTERNAL-NODE-003

- 动态 body Schema 被下游引用时必须 VALID 才能生成版本。

### EXTERNAL-NODE-004

- Notify 成功只表示提交被接受，不表示最终送达。

### EXTERNAL-NODE-005

- Notify 不向下游暴露业务输出。

### EXTERNAL-NODE-006

- Draft 默认不得真实外联；真实模式必须显式环境、权限、确认和审计。

### EXTERNAL-NODE-007

- 所有外联必须遵守连接器安全、超时、大小和重试限制。

### EXTERNAL-NODE-008

- 日志必须记录脱敏目标、配置指纹和实际凭据版本，不记录密文。

## 7. Acceptance Outcomes

- HTTP 能解析结构化 JSON 和文本响应，并管理输出 Schema 状态。
- 连接器或凭据变化按资源治理规则生效。
- Notify 能提交四类 MVP 通道并区分同步提交失败。
- 普通 Draft 测试不会意外真实发送。
- 真实测试和生产执行均留下审计/执行摘要。
- 禁止网络目标和超大响应被拒绝。

## 8. Dependencies

- CHG-03
- CHG-06
- CHG-11

## 9. Handoff Guarantees to Later Changes

- 完整 MVP 具备外部事实查询和通知能力。
- Function 无需承担外部 IO。
- Prod 发布可对外联资源执行一致性校验。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
