# CHG-04 — 实现 Request、Decision Table、Response 最小决策链路

## 1. Change Goal

实现从输入 Schema、决策表求值到共享响应契约的首条完整可执行纯计算链路。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-02、CHG-03 已归档。
- Zen-derived 核心模型 Spike 已确认可行路径。

## 4. In Scope

- Request 节点完整配置、校验和运行。
- Decision Table 输入列、输出列、规则行、首条命中和可解释结果。
- Response 单一返回结构、共享输出契约和字段映射。
- 最小产品模型到执行模型转换。
- 通过固定 fixture 或内部执行入口运行 `Request -> Decision Table -> Response`。

## 5. Out of Scope

- Profile、List、Counter。
- 控制台节点验证和整流测试工作台。
- Flow Version、环境 Artifact 和对外 API。
- Response 多返回结构。

## 6. Normative Behavior Requirements

### CORE-NODE-001

- 流程必须有且只有一个 Request，至少一个 Response。

### CORE-NODE-002

- Request 未声明字段不得进入变量空间，rawRequest 不得默认持久化。

### CORE-NODE-003

- Decision Table 必须支持绑定列、未绑定列、多个输出列和首条命中。

### CORE-NODE-004

- 全空规则行只允许最后一行；无命中时输出列全部为 null。

### CORE-NODE-005

- Decision Table 类型不匹配和表达式失败必须严格失败。

### CORE-NODE-006

- 所有 Response 必须共享同一单一返回契约并完成必填映射。

### CORE-NODE-007

- 平台 request id 通过响应元数据返回，不强制加入业务响应字段。

### CORE-NODE-008

- 执行结果必须包含命中行、逐列判定和响应节点的可解释摘要。

## 7. Acceptance Outcomes

- 固定输入可通过 Decision Table 命中不同规则并返回预期 Response。
- 无命中、全空兜底、类型错误和缺失必填映射均有确定结果。
- Request 未声明字段不能被 Decision Table 引用。
- 节点改名不影响已保存映射。
- 执行结果可以关联产品节点和运行节点。

## 8. Dependencies

- CHG-02
- CHG-03

## 9. Handoff Guarantees to Later Changes

- 后续事实节点可以插入 Request 与 Decision Table 之间。
- 后续测试、版本和发布 Change 已有可编译的最小决策模型。
- Decision Table 不会被延后到其他 Change 而造成范围断裂。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
