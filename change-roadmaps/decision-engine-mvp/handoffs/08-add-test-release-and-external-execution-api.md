# CHG-08 — 实现 Test 申请、手动发布与外部执行 API

## 1. Change Goal

完成从 Flow Version 到 Test 当前生效版本的治理闭环，并允许客户使用 Test 环境 Key 调用生效 Artifact。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-07 已归档。

## 4. In Scope

- Test 发布申请、系统自动审批和完整记录。
- Test 手动发布、Deployment 状态、Active Pointer 和失败保护。
- 版本与发布页面的 Test 环境视图。
- 版本化外部执行 API。
- Test 环境 bootstrap Key 的创建时一次展示、hash 校验和指纹。
- 基础限流、request id、幂等上下文、系统错误和执行日志写出。

## 5. Out of Scope

- Prod 审批、发布和回滚。
- 完整 API Key current/next/retiring 生命周期 UI。
- 成员角色和高风险能力。
- 控制台执行日志检索。

## 6. Normative Behavior Requirements

### TEST-REL-001

- Test 申请必须自动审批但不得自动发布。

### TEST-REL-002

- 申请必须冻结版本、校验和目标环境资源摘要。

### TEST-REL-003

- 手动发布必须使用幂等 Deployment，并原子切换 Test Active Pointer。

### TEST-REL-004

- 发布失败必须保留上一 Test Artifact。

### TEST-REL-005

- 外部 API 环境必须由 Test Key 决定，客户端不得切换到其他环境。

### TEST-REL-006

- Key 完整值只在创建时显示，数据库只存 hash 和指纹。

### TEST-REL-007

- 业务 Response 与平台系统错误必须分离，响应必须可关联 request id。

### TEST-REL-008

- Counter 幂等上下文必须从 API 请求传入运行时。

## 7. Acceptance Outcomes

- Test 申请自动通过后停留在待发布状态。
- 发布成员手动发布后外部调用命中新 Artifact。
- 失败发布后外部调用继续命中旧 Artifact。
- 无效 Key、未部署 Flow、Schema 错误、限流和节点失败返回稳定平台错误。
- Key 明文无法在创建后重新读取。
- 同一幂等请求重试不重复记录 Counter。

## 8. Dependencies

- CHG-07

## 9. Handoff Guarantees to Later Changes

- P0 端到端闭环完成。
- 后续完整 API Key 生命周期可以迁移 bootstrap Key 而不改变执行 API。
- Prod 治理可以复用 Release Request、Deployment 和 Pointer。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
