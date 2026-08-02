# Requirement Coverage Matrix

## 1. 使用方式

本矩阵按阶段 1 PRD/Architect 的稳定 Requirement Group 追踪主 Change。详细场景由对应 Handoff 和后续 OpenSpec specs 展开。

标记：

- **Primary**：该 Change 建立主要行为。
- **Extend**：该 Change 扩展或治理已有行为。
- **Verify**：该 Change 完成系统级验证和硬化。

## 2. PRD 覆盖

| Requirement Group | Primary | Extend / Verify | 说明 |
| --- | --- | --- | --- |
| PRD-GOAL-* | CHG-01～CHG-08 | CHG-09～CHG-16 | 全 Roadmap |
| PRD-NONGOAL-* | 无实现 Change | CHG-16 Verify | 用于防止范围膨胀 |
| PRD-P0-* | CHG-01～CHG-08 | CHG-16 Verify | P0 完整闭环 |
| PRD-P1-* | CHG-09～CHG-16 | CHG-16 Verify | 完整 MVP 扩展 |
| PRD-P0-001 | CHG-01 | CHG-10、CHG-16 | bootstrap 后升级成员体系 |
| PRD-P0-002 | CHG-02 | CHG-07 | Draft/Version |
| PRD-P0-003 | CHG-04、CHG-05 | CHG-06、CHG-07 | 六节点 |
| PRD-P0-004 | CHG-05 | CHG-16 | 画像和环境名单 |
| PRD-P0-005 | CHG-05 | CHG-06、CHG-08、CHG-16 | Counter 闭环 |
| PRD-P0-006 | CHG-06 | CHG-09、CHG-12、CHG-13、CHG-15 | 验证框架扩展 |
| PRD-P0-007 | CHG-07 | CHG-11、CHG-16 | Version/Artifact |
| PRD-P0-008 | CHG-08 | CHG-14 | Test 到 Prod |
| PRD-P0-009 | CHG-08 | CHG-10、CHG-16 | 执行 API 与 Key |
| PRD-P0-010 | CHG-01、CHG-08 | CHG-16 | 最小到完整审计/日志 |
| PRD-P1-001 | CHG-09 | CHG-16 | Derived/Condition |
| PRD-P1-002 | CHG-11 | CHG-12、CHG-14 | 资源与凭据 |
| PRD-P1-003 | CHG-12 | CHG-16 | HTTP/Notify |
| PRD-P1-004 | CHG-13 | CHG-16 | List 写 |
| PRD-P1-005 | CHG-10 | CHG-11～CHG-15 | 权限和 Key |
| PRD-P1-006 | CHG-14 | CHG-16 | Prod |
| PRD-P1-007 | CHG-15 | CHG-16 | Function |
| PRD-P1-008 | CHG-16 | — | Hardening |
| PRD-P1-009 | CHG-09 | CHG-16 | Response 多返回结构 |
| PRD-P1-010 | CHG-16 | — | 完整准入 |
| PRD-DOMAIN-* | CHG-02、CHG-07 | CHG-08、CHG-11、CHG-14 | 项目、Draft、Version、Artifact、资源 |
| PRD-FLOW-* | CHG-02、CHG-03 | CHG-04、CHG-06、CHG-09 | 图、变量、错误 |
| PRD-AUTHOR-* | CHG-02、CHG-03、CHG-06 | 各节点 Change | 编辑、Value Editor、门禁 |
| PRD-TEST-* | CHG-06 | CHG-12、CHG-13、CHG-15 | 测试和副作用 |
| PRD-NODE-REQUEST-* | CHG-04 | — | 请求契约 |
| PRD-NODE-RESPONSE-* | CHG-04 | CHG-09 | 单一到多返回 |
| PRD-NODE-DTABLE-* | CHG-04 | — | 决策表 |
| PRD-NODE-PROFILE-* | CHG-05 | — | 画像 |
| PRD-NODE-LIST-* | CHG-05 | CHG-13 | 查询与写 |
| PRD-NODE-COUNTER-* | CHG-05 | CHG-16 Verify | Counter |
| PRD-NODE-DERIVED-* | CHG-09 | — | 派生变量 |
| PRD-NODE-CONDITION-* | CHG-09 | — | 条件路由 |
| PRD-NODE-HTTP-* | CHG-12 | — | HTTP |
| PRD-NODE-NOTIFY-* | CHG-12 | — | Notify |
| PRD-NODE-FUNCTION-* | CHG-15 | — | Function |
| PRD-RESOURCE-PROFILE-* | CHG-05 | — | 内置画像 |
| PRD-RESOURCE-LIST-* | CHG-05 | CHG-13 | 环境名单 |
| PRD-RESOURCE-HTTP-* | CHG-11 | CHG-12 | HTTP 连接器 |
| PRD-RESOURCE-CHANNEL-* | CHG-11 | CHG-12 | 通道连接器 |
| PRD-RESOURCE-CREDENTIAL-* | CHG-11 | CHG-12、CHG-14 | 凭据 |
| PRD-RESOURCE-APIKEY-* | CHG-08 | CHG-10 | bootstrap 到完整生命周期 |
| PRD-RELEASE-* | CHG-07、CHG-08、CHG-14 | CHG-16 Verify | Dev/Test/Prod/rollback |
| PRD-SECURITY-* | CHG-01、CHG-05、CHG-10、CHG-11 | CHG-12～CHG-16 | 权限、隔离、egress |
| PRD-AUDIT-* | CHG-01 | CHG-08、CHG-10～CHG-16 | 审计 |
| PRD-API-* | CHG-08 | CHG-10、CHG-16 | 对外 API |
| PRD-OBS-* | CHG-08 | CHG-16 | 日志指标 |
| PRD-NFR-* | 各功能 Change | CHG-16 Verify | 非功能准入 |

## 3. Architect 覆盖

| Architecture Group | Primary Change | 说明 |
| --- | --- | --- |
| ARC-INV-001～008 | CHG-01、CHG-07 | 应用边界、事实源、缓存 |
| ARC-INV-009～012 | CHG-07 | Version/Artifact/Pointer |
| ARC-INV-013～016 | CHG-02、CHG-03 | Canonical model |
| ARC-INV-017～019 | CHG-03 | Expression/error |
| ARC-INV-020～024 | CHG-01、CHG-10、CHG-11 | 隔离与安全 |
| ARC-CTRL-* | CHG-01 | 后续模块复用 |
| ARC-COMP-* | CHG-03、CHG-07 | 编译与 Schema |
| ARC-REL-* | CHG-07、CHG-08、CHG-14 | 发布一致性 |
| ARC-CONSISTENCY-* | CHG-02、CHG-05、CHG-07、CHG-08、CHG-14 | 并发幂等 |
| ARC-RISK-* | Stage 3 Spikes | 结果路由至对应 Change |

## 4. 特殊覆盖项

### Response 多返回结构

PRD-P1-009 由 CHG-09 与 Condition、多 Response 一起实现。CHG-04 只建立 P0 单一结构，避免把分支与多结构复杂度提前带入核心三节点切片。

### 最小与完整审计

CHG-01 建立事务内最小审计；各业务 Change 写入事件；CHG-16 建立查询、富化、补偿和系统准入。任何中间 Change 不得以“最终会在 CHG-16 完成”为由完全跳过审计事件。

### 最小与完整 API Key

CHG-08 只提供 P0 可调用的 Test bootstrap Key；CHG-10 迁移到完整环境级生命周期。CHG-08 不得提前实现一套无法迁移的临时密钥模型。

## 5. Coverage 门禁

进入 CHG-01 propose 前：

- 所有 P0 Requirement Group 已有 Primary Change。
- 无 Change 依赖未覆盖的资源或平台能力。
- 无循环依赖。
- Stage 3 Index 为每个 Change 指定全局工程输入。
- Blocking Spike 有明确 owner 和完成时点。

进入 CHG-16 verify 前：

- 所有 P1 Requirement Group 有已归档 Change 或经批准的 Framework 修订。
- 自动化测试能够回链到 Requirement Group。
- 所有特殊覆盖项已关闭。
