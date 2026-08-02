# CHG-14 — 实现 Prod 审批、发布、失败恢复与回滚

## 1. Change Goal

在 Test 已验证版本、资源指纹和权限体系上完成 Prod 的人工审批、手动发布、并发保护、失败恢复和回滚。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-08、CHG-10、CHG-11 已归档。

## 4. In Scope

- 审批中心列表和详情。
- Prod 发布申请、风险摘要、通过、驳回和撤销。
- Prod 手动发布和 Deployment 状态机。
- 资源重新解析和指纹一致性。
- 失败详情、显式重试和回滚。
- 版本与发布页面 Prod 环境卡。

## 5. Out of Scope

- 多人会签、评论流、附件和批量审批。
- 灰度和 A/B。
- 自动发布到 Prod。

## 6. Normative Behavior Requirements

### PROD-REL-001

- 只有同一 Flow Version 已成功发布 Test 才能申请 Prod。

### PROD-REL-002

- 审批快照必须冻结版本、Diff、校验、风险和 Prod 资源指纹。

### PROD-REL-003

- 驳回理由必填，撤销只允许待审批申请。

### PROD-REL-004

- 审批通过不等于发布，必须由发布成员手动执行。

### PROD-REL-005

- 发布前资源指纹变化必须阻止发布并要求新申请。

### PROD-REL-006

- 同 flow/environment 发布、重试和回滚必须串行化。

### PROD-REL-007

- 失败不得修改当前指针，重试必须幂等并保留历史尝试。

### PROD-REL-008

- 回滚必须切换到历史成功 Artifact 并形成新 Deployment 和审计。

## 7. Acceptance Outcomes

- 未经 Test 的版本无法申请 Prod。
- 审批成员和发布成员权限分别生效。
- 资源变化会阻断旧审批发布。
- 并发发布只有一个成功进入切换。
- 发布失败后旧 Prod 继续服务。
- 回滚后外部调用命中历史 Artifact，记录完整。

## 8. Dependencies

- CHG-08
- CHG-10
- CHG-11

## 9. Handoff Guarantees to Later Changes

- 完整多环境发布治理闭环建立。
- 后续硬化可以围绕稳定状态机、审计和指标进行。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
