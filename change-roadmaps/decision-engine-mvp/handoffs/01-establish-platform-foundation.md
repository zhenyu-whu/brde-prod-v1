# CHG-01 — 建立多租户平台与运行骨架

## 1. Change Goal

建立可启动、可迁移、可观测的控制台、控制面、异步任务与执行面骨架，为后续所有产品能力提供统一租户上下文、请求关联和基础数据设施。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- 阶段 1 PRD 与 Architect 已收口。
- 关键技术选型未被新的 ADR 推翻。

## 4. In Scope

- 建立控制台、控制面、worker、执行面和本地基础设施的可运行边界。
- 建立租户、bootstrap 管理员、登录会话和不可伪造的 tenant/actor context。
- 建立数据库迁移、健康检查、请求 ID、trace context、基础错误结构和最小审计事件基础。
- 建立内部 RPC 代码生成和兼容性检查的基础链路。
- 提供本地画像源、外联和执行依赖的可控 stub 入口。

## 5. Out of Scope

- 决策流、Draft、节点和画布。
- 完整成员邀请、角色、高风险能力和 API Key 生命周期。
- 业务版本、发布、资源和执行日志完整能力。

## 6. Normative Behavior Requirements

### PLATFORM-001

- 系统必须能够在本地通过统一方式启动所有应用和基础依赖，并分别暴露 readiness 与 liveness。

### PLATFORM-002

- 所有控制面请求必须解析 tenant、actor、request id；缺失或不一致时不得进入业务逻辑。

### PLATFORM-003

- P0 可以只有 bootstrap 管理员，但数据模型不得假设系统永远只有一个用户或租户。

### PLATFORM-004

- 控制面与执行面必须保持独立进程和调用边界，客户请求不得经过控制面。

### PLATFORM-005

- 所有应用必须能够传播 trace context，并使用统一平台错误分类的基础结构。

### PLATFORM-006

- 关键基础写操作必须同时写入最小审计事件；审计内容不得包含密文。

### PLATFORM-007

- 内部协议必须版本化并纳入自动兼容性检查。

## 7. Acceptance Outcomes

- 新环境可重复启动并完成数据库迁移。
- bootstrap 管理员能够登录并只看到所属租户。
- 跨租户访问测试被拒绝。
- 控制面、worker、executor 的健康状态和 trace 可以关联。
- 一次基础控制面写入同时产生最小审计事件。

## 8. Dependencies

- 无前置 Change。

## 9. Handoff Guarantees to Later Changes

- 后续 Change 可以依赖统一 tenant/actor/request context。
- 后续 Change 可以依赖控制面、worker、executor 和数据库的稳定应用边界。
- 后续 Change 可以增加领域模型而无需重建基础运行骨架。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
