# CHG-05 — 补齐画像、名单查询与 Counter 事实能力

## 1. Change Goal

提供注册防刷六节点主链路所需的全部资源和事实节点，并形成连续请求可验证的 Counter 统计闭环。

## 2. Capability Outcome

本 Change 完成并归档后，系统应形成一个可由后续 Change 直接依赖、无需重新定义的能力边界。该能力必须以可观察行为、稳定契约和验收结果为准，而不是以某个特定类、表或组件是否存在为准。

## 3. Preconditions

- CHG-04 已归档。
- Counter Spike 已形成原子性和容量结论。

## 4. In Scope

- 系统内置画像源目录、字段目录版本和测试适配器。
- 按环境隔离的名单资源定义、单 key 查询和 List(query) 节点。
- Counter 计次、分组去重、窗口、幂等和 read-before-write 语义。
- Profile Query 和 List 的失败/降级输出。
- 六节点链路的编译和执行支持。

## 5. Out of Scope

- List add/remove/refresh_ttl。
- HTTP、Notify、用户自定义画像源。
- 控制台节点验证和整流测试。
- 正式版本和环境发布。

## 6. Normative Behavior Requirements

### FACT-001

- Profile Query 必须只引用系统内置画像源，输出 success/degraded/reason/payload。

### FACT-002

- 画像字段目录必须有稳定版本，并在选择画像源后形成可用输出 Schema。

### FACT-003

- 名单必须按 tenant、environment、list 隔离，不得跨环境隐式共享。

### FACT-004

- List(query) 空输入必须返回未命中和 empty_input，而不是系统错误。

### FACT-005

- Counter 必须原子读取历史值、输出历史值并记录当前请求。

### FACT-006

- 同一幂等请求不得重复增加 Counter。

### FACT-007

- Counter 状态必须按 tenant、flow、environment 和统计项隔离。

### FACT-008

- 事实节点只能提供事实数据，最终风险判定由 Decision Table 完成。

## 7. Acceptance Outcomes

- 用户能够查看画像目录并创建 Dev/Test/Prod 各自名单。
- 六节点主链路可以成功运行。
- 连续两次不同 request id 调用时，第二次看到第一请求产生的历史计数。
- 同一 request id 重试不重复计数。
- Test 名单变化不影响 Prod 名单。
- 画像降级和名单空输入按约定输出。

## 8. Dependencies

- CHG-04

## 9. Handoff Guarantees to Later Changes

- P0 注册防刷的全部节点和资源前置能力均已存在。
- CHG-06 不需要临时伪造 Profile/List 资源。
- 后续 List 写操作可以在现有资源和输出模型上扩展。

## 10. Propose Boundary

后续 OpenSpec propose 必须：

- 读取本 Handoff；
- 读取 `engineering/02-change-engineering-index.yaml` 为本 Change 指定的全局工程章节和 Spike 状态；
- 读取当前 `openspec/specs/` 与代码；
- 在 Change Artifacts 中冻结本次采用的行为和技术设计；
- 不把 Out of Scope 内容吸收到本 Change；
- 若发现上游产品语义、Framework 或全局工程基线错误，先回流修订相应文档，不得在 `design.md` 中静默改写。
