# Change Capability Framework

## 1. 拆分原则

1. 每个 Change 必须产生可独立验证的 Capability Outcome。
2. 用户功能与必要平台行为都可以成为 Capability；纯实现任务不得成为独立 Change。
3. P0 优先形成六节点真实垂直切片，再扩展完整 MVP。
4. 共享模型必须先于消费它的节点和页面。
5. 正式权限体系必须先于高风险外联、名单写和 Prod 治理。
6. Handoff 只定义语义边界；具体技术设计由 Stage 3 基线和当前 propose 完成。

## 2. Change 总览

| ID | Change | 阶段 | 直接依赖 | 核心交付 |
| --- | --- | --- | --- | --- |
| CHG-01 | 建立多租户平台与运行骨架 | Foundation | 无 | 建立可启动、可迁移、可观测的控制台、控制面、异步任务与执行面骨架，为后续所有产品能力提供统一租户上下文、请求关联和基础数据设施。 |
| CHG-02 | 建立决策流领域模型与画布编辑骨架 | Foundation | CHG-01 | 建立项目分类、决策流、唯一 Draft、canonical graph 和桌面画布编辑骨架，使后续节点能力都能在同一 Draft 与拓扑模型上扩展。 |
| CHG-03 | 建立值、表达式、变量与输出 Schema 契约 | Foundation | CHG-01、CHG-02 | 建立跨前端、控制面和 Go 执行面的统一值类型、稳定变量引用、表达式子集、错误语义和输出 Schema 生命周期。 |
| CHG-04 | 实现 Request、Decision Table、Response 最小决策链路 | P0 Core | CHG-02、CHG-03 | 实现从输入 Schema、决策表求值到共享响应契约的首条完整可执行纯计算链路。 |
| CHG-05 | 补齐画像、名单查询与 Counter 事实能力 | P0 Core | CHG-04 | 提供注册防刷六节点主链路所需的全部资源和事实节点，并形成连续请求可验证的 Counter 统计闭环。 |
| CHG-06 | 实现 Draft 静态门禁、节点验证与整流测试 | P0 Core | CHG-05 | 让策略师在生成版本前能够定位静态问题、执行到指定节点并运行当前 Draft 的完整路径，同时保证测试状态与生效环境隔离。 |
| CHG-07 | 实现不可变版本、环境 Artifact 与 Dev Active Build | P0 Governance | CHG-06 | 将通过门禁的 Draft 冻结为环境无关 Flow Version，并建立环境相关 Artifact、编译 hash、Diff、资源指纹和 Dev 显式生效。 |
| CHG-08 | 实现 Test 申请、手动发布与外部执行 API | P0 Governance | CHG-07 | 完成从 Flow Version 到 Test 当前生效版本的治理闭环，并允许客户使用 Test 环境 Key 调用生效 Artifact。 |
| CHG-09 | 增加 Derived、Condition 与多返回响应 | MVP Extension | CHG-08 | 在既有单路径图模型上增加可命名中间计算、显式互斥分支和 Response 多返回结构，同时保持无汇合和单路径执行。 |
| CHG-10 | 完善成员、角色、高风险能力与 API Key 生命周期 | MVP Governance | CHG-08 | 从 bootstrap 管理员升级为可治理的租户成员体系，并为已实现的编辑、发布、资源、名单和 Key 操作实施统一授权。 |
| CHG-11 | 建立连接器、凭据与环境资源治理平台 | MVP Extension | CHG-07、CHG-10 | 建立 HTTP 连接器、通道连接器、凭据版本、环境绑定、受保护资源和资源指纹，使外联节点能够安全引用共享资源。 |
| CHG-12 | 实现 HTTP 与 Notify 外联节点 | MVP Extension | CHG-03、CHG-06、CHG-11 | 在连接器和测试安全模型上实现外部 HTTP 查询与通知提交，并保持可解释、受限和可审计。 |
| CHG-13 | 补齐名单写操作与统一副作用安全 | MVP Extension | CHG-05、CHG-06、CHG-10 | 在既有 List 资源和测试模式上增加 add/remove/refresh_ttl，并建立对状态副作用的一致权限、幂等、确认和审计行为。 |
| CHG-14 | 实现 Prod 审批、发布、失败恢复与回滚 | MVP Governance | CHG-08、CHG-10、CHG-11 | 在 Test 已验证版本、资源指纹和权限体系上完成 Prod 的人工审批、手动发布、并发保护、失败恢复和回滚。 |
| CHG-15 | 实现 Function 节点与隔离运行时 | MVP Extension | CHG-03、CHG-06、CHG-07、CHG-10 | 提供受控复杂数据转换能力，同时保证多租户实时执行的资源、安全和可解释边界。 |
| CHG-16 | 完成审计、执行日志、指标、可靠性与 MVP 硬化 | MVP Hardening | CHG-09、CHG-12、CHG-13、CHG-14、CHG-15 | 将各阶段已建立的最小审计、日志和指标扩展为可运营、可恢复并通过完整 MVP 准入测试的系统。 |

## 3. P0 边界

P0 由 CHG-01 至 CHG-08 构成。CHG-08 完成后，系统必须能够从空白项目创建六节点 Draft，完成测试、版本生成、Test 自动审批和手动发布，并通过 Test Key 连续调用以验证 Counter 闭环。

任何 P0 Change 不得假设后续资源、权限或节点已经存在。特别是：

- CHG-04 在同一 Change 内完整实现 Decision Table，而不是只把它写入 In Scope。
- CHG-05 同时建立内置画像源目录和名单资源，Profile/List 节点不依赖未实现资源。
- CHG-06 建立测试状态隔离，不用生效环境数据替代 Draft 测试。
- CHG-08 提供最小 Test Key 能力，外部 API 不依赖尚未完成的完整 Key 管理。

## 4. 完整 MVP 边界

CHG-09 至 CHG-16 在 P0 契约之上补齐分支与多返回响应、正式权限与 Key 生命周期、连接器、外联节点、名单副作用、Prod、Function 和硬化。CHG-09 和 CHG-10 均以 P0 完成为起点，高风险资源与节点在正式权限模型完成后进入。

## 5. Change 交付状态

| 状态 | 含义 |
| --- | --- |
| framework-ready | Handoff 已生成，依赖和范围已确认 |
| engineering-ready | Stage 3 Index 指定输入可用，阻塞 Spike 已完成 |
| proposed | OpenSpec Artifacts 已通过一致性 Review |
| applied | 代码和迁移已实现 |
| verified | 验收、测试和风险检查通过 |
| archived | Change 已归档并更新当前 specs |

## 6. Framework 修订规则

出现以下情况必须修订 Framework 或 Handoff，而不是只在某个 Change `design.md` 中绕过：

- 新发现的产品行为改变 Change 能力边界；
- 后续能力要求早期 Change 提供新的稳定契约；
- Spike 推翻 Architect 中的关键假设；
- 两个 Change 对同一共享契约给出不兼容答案；
- Coverage Matrix 出现遗漏、重复或循环依赖。
