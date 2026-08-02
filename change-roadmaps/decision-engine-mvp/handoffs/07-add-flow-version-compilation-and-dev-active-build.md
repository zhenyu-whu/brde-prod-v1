# CHG-07 — 实现不可变版本、环境 Artifact 与 Dev Active Build

## 1. Change Goal

将通过门禁的 Draft 冻结为环境无关 Flow Version，并建立环境相关 Artifact、编译 hash、Diff、资源指纹和 Dev 显式生效。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-06 已归档。
- Artifact 分发 Spike 已给出可行方案。

## 4. In Scope

- Flow Version 生成和只读浏览。
- 环境相关 Compiled Artifact。
- Draft 到 Dev Active Build 的显式发布。
- 版本 Diff、节点/Schema/Decision Table 变化摘要。
- 资源逻辑引用和目标环境指纹。
- Artifact 预热、缓存失效和 Dev Active Pointer。

## 5. Out of Scope

- Test/Prod 申请和审批。
- 外部执行 API。
- 完整连接器和凭据资源。

## 6. Normative Behavior Requirements

### VERSION-001

- Flow Version 必须绑定明确 Draft revision，并保持环境无关和不可变。

### VERSION-002

- Compiled Artifact 必须绑定目标环境、编译器版本、资源解析结果和稳定 hash。

### VERSION-003

- 保存 Draft 不得改变 Dev Active Build。

### VERSION-004

- 发布到 Dev 必须重新校验并原子切换；失败时保留上一 Build。

### VERSION-005

- 版本 Diff 必须覆盖节点、连线、表达式、Schema、Decision Table 和资源引用。

### VERSION-006

- 从历史版本重建 Draft 必须显式确认并产生新 Draft revision。

### VERSION-007

- 动态输出 Schema 必须冻结进版本，不得依赖最近浏览器会话。

## 7. Acceptance Outcomes

- 同一 Draft revision 生成的 Flow Version 不再可编辑。
- 同一 Flow Version 可以为不同环境生成不同 Artifact。
- Dev 发布失败不会中断上一 Build。
- 从历史版本重建 Draft 后历史版本不变。
- Artifact hash 和编译器版本可追溯。

## 8. Dependencies

- CHG-06

## 9. Handoff Guarantees to Later Changes

- 后续 Test/Prod 发布建立在同一版本和 Artifact 模型上。
- executor 可以通过环境 Active Pointer 加载不可变产物。
- 资源平台可以将环境解析和指纹接入既有编译流程。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
