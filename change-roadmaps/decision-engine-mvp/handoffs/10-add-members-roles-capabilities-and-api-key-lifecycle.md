# CHG-10 — 完善成员、角色、高风险能力与 API Key 生命周期

## 1. Change Goal

从 bootstrap 管理员升级为可治理的租户成员体系，并为已实现的编辑、发布、资源、名单和 Key 操作实施统一授权。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-08 已归档。

## 4. In Scope

- 成员邀请、停用和租户全局角色。
- 高风险能力授予、收回和二次确认。
- 对既有控制面动作的权限实施。
- Dev/Test/Prod API Key 的 NEXT/CURRENT/RETIRING/REVOKED 生命周期。
- Key 创建时一次展示、宽限期、紧急撤销和最后使用摘要。

## 5. Out of Scope

- 项目级、Flow 级、环境级授权矩阵。
- OIDC/SAML。
- 自定义角色编辑器。
- Prod 发布审批流程本身。

## 6. Normative Behavior Requirements

### ACCESS-001

- 只读、编辑、发布、审批、管理员角色必须具有明确最小能力。

### ACCESS-002

- 审批角色不得自动获得发布，发布角色不得自动获得审批。

### ACCESS-003

- Function、凭据、连接器、名单、Key、权限和真实外联测试必须使用高风险能力。

### ACCESS-004

- 所有权限变化必须二次确认并审计。

### ACCESS-005

- Key 完整值只在创建时显示，之后不能恢复。

### ACCESS-006

- NEXT 提升后旧 CURRENT 进入 RETIRING，在宽限期内继续有效。

### ACCESS-007

- 紧急撤销必须具有明确最大传播时间。

### ACCESS-008

- 现有 bootstrap 管理员和 Test Key 必须可平滑迁移。

## 7. Acceptance Outcomes

- 不同角色只能执行授权操作。
- 同一用户可同时具有审批和发布，但权限独立。
- Key 轮换期间新旧 Key 重叠有效，宽限期后旧 Key 失效。
- Key 明文无法重新查看。
- 紧急撤销在约定时间内对 executor 生效。
- 全部权限和 Key 操作进入审计。

## 8. Dependencies

- CHG-08

## 9. Handoff Guarantees to Later Changes

- Prod 治理可以依赖真实审批人与发布人权限。
- Function 和真实外联测试有统一高风险授权。
- 环境 API Key 达到完整 MVP 生命周期。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
