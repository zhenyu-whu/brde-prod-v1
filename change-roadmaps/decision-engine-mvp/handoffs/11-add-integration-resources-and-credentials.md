# CHG-11 — 建立连接器、凭据与环境资源治理平台

## 1. Change Goal

建立 HTTP 连接器、通道连接器、凭据版本、环境绑定、受保护资源和资源指纹，使外联节点能够安全引用共享资源。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-07、CHG-10 已归档。
- CHG-01 的密钥和审计基础可用。

## 4. In Scope

- HTTP 连接器和通道连接器资源模型与控制台。
- 显式环境绑定和 Dev/Test 不回退 Prod。
- 凭据对象、版本、active 切换和加密。
- 连接器连通性测试。
- 资源引用、受保护状态、复制为新资源和影响范围。
- 目标环境资源解析和 canonical fingerprint。

## 5. Out of Scope

- HTTP/Notify 节点。
- 名单写操作。
- 完整成员权限和 API Key 生命周期。

## 6. Normative Behavior Requirements

### RESOURCE-001

- 每个启用环境必须解析到明确运行绑定，Dev/Test 不得隐式使用 Prod。

### RESOURCE-002

- 节点和资源页面不得展示明文凭据。

### RESOURCE-003

- 凭据 active 版本可以独立切换，并记录实际使用版本指纹。

### RESOURCE-004

- 被 Test/Prod 当前生效 Artifact 引用的资源行为字段不得原地修改。

### RESOURCE-005

- 受保护资源变更必须复制新资源并在 Draft 切换引用。

### RESOURCE-006

- 资源指纹只包含环境解析后的行为字段和稳定绑定，不包含显示元数据。

### RESOURCE-007

- 真实连通性测试必须经过 egress 安全、权限、二次确认和审计。

### RESOURCE-008

- 资源变更必须可展示对 Draft 和生效环境的影响范围。

## 7. Acceptance Outcomes

- 用户能创建各环境连接器并绑定凭据。
- Dev/Test 不能解析到 Prod 地址。
- 凭据轮换后不重发版本也可执行，并能追溯新版本。
- 受保护资源原地修改被阻止。
- 资源指纹变化能被版本/发布流程识别。
- SSRF 禁止目标和敏感日志测试通过。

## 8. Dependencies

- CHG-07
- CHG-10

## 9. Handoff Guarantees to Later Changes

- HTTP 和 Notify 节点可以只引用连接器，不接触密文。
- Prod 发布可以使用稳定资源解析和指纹。
- 凭据轮换与 Flow Version 生命周期已解耦。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
