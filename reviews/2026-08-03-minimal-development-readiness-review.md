# BRDE 产品开发启动最小化 Review

- 评审目标：基于现有 PRD 与 Architect 文档，在保持完整业务功能范围的前提下，补充进入开发阶段所需的最小设计缺口。
- 非目标：本轮不解决生产级 SaaS 能力，包括高可用、灾备、复杂安全、多租户商业化治理、极限性能优化等。

## 总体结论

现有 PRD 和 Architect 已具备完整产品方向和主要功能描述，可以进入开发阶段。

当前主要缺口不是产品范围，而是部分业务对象、状态流转和工程边界没有被明确描述，容易导致开发过程中产生大量二次设计。

建议只补充以下 12 项内容即可进入功能开发。

---

# 一、PRD 最小补充项

## PRD-01 产品对象模型

补充核心对象关系：

```
Decision Flow
  ├── Draft
  ├── Version
  │     └── Artifact
  └── Release(Test/Prod)

Resource
  ├── Profile Source
  ├── List
  └── Connector

Execution
  ├── Request
  ├── Trace
  └── Response
```

目标：统一产品、前后端和数据模型理解。

---

## PRD-02 核心状态机

补充主要业务状态。

Version：

```
Draft
  ↓
Version Created
  ↓
Testing
  ↓
Released
  ↓
Rollback
```

Approval：

```
Pending
  ↓
Approved / Rejected / Cancelled
```

目标：保证业务流程可实现。

---

## PRD-03 用户业务流程

补充端到端用户 Journey：

```
创建决策流
 ↓
配置节点
 ↓
测试执行
 ↓
生成版本
 ↓
发布环境
 ↓
API 调用
```

以及策略变更流程：

```
修改 Draft
 ↓
生成新 Version
 ↓
查看 Diff
 ↓
发布
```

---

## PRD-04 节点统一规范

所有节点统一描述：

```
Node
 ├── id
 ├── type
 ├── config
 ├── input
 ├── output
 ├── validation
 └── runtime
```

每个节点必须明确：

- 输入变量
- 输出变量
- 配置项
- 校验规则
- 执行逻辑

---

## PRD-05 资源生命周期

统一资源管理语义：

```
Created
 ↓
Active
 ↓
Referenced
 ↓
Protected
 ↓
Deprecated
```

覆盖：

- List
- HTTP Connector
- Notify Connector
- Credential

---

## PRD-06 测试语义

明确三类测试：

|类型|目的|
|-|-|
|节点测试|验证单节点配置|
|流程测试|验证完整 Draft|
|环境运行|验证发布版本|

避免测试结果和线上执行语义混淆。

---

## PRD-07 功能验收标准

每个核心能力补充完成标准：

例如决策流：

- 可以创建
- 可以编辑
- 可以测试
- 可以生成版本
- 可以发布
- 可以被 API 调用

---

# 二、Architect 最小补充项

## ARCH-01 业务领域划分

补充业务模块边界：

- Decision Flow Domain
- Version Domain
- Release Domain
- Resource Domain
- Execution Domain
- Identity Domain
- Audit Domain

---

## ARCH-02 核心数据模型

补充最小数据模型：

```
flow
 draft
 version
 artifact
 release
 resource
 execution
```

不要求完整 ER，只需明确关键字段和关系。

---

## ARCH-03 Compiler 边界

明确：

输入：

```
Flow Config
Node Config
Resource Reference
```

输出：

```
Executable Artifact
```

Compiler 负责转换和校验，不负责运行。

---

## ARCH-04 执行链路

明确运行流程：

```
Client
 ↓
Executor
 ↓
Load Artifact
 ↓
Execute Nodes
 ↓
Generate Trace
 ↓
Response
```

测试流程：

```
Console
 ↓
Control API
 ↓
Compiler
 ↓
Executor
```

---

## ARCH-05 API 契约原则

补充最小 API 规范：

请求：

```
POST /decision/{flowId}/execute
```

响应：

```
{
 result,
 traceId
}
```

统一定义：

- Request
- Response
- Error

---

# 三、明确后续阶段优化

以下不阻塞当前开发：

## 稳定性

- 多副本
- 容灾
- 灾备
- RPO/RTO

## 安全

- KMS/HSM
- 零信任
- mTLS
- 高级审计

## SaaS 商业能力

- 企业 SSO
- 配额体系
- 商业套餐
- 租户生命周期管理

## 高级治理

- 灰度发布
- A/B 实验
- 策略分析
- 回放系统

---

# 开发启动 Gate

满足以下条件即可进入实现：

1. 产品对象和状态明确。
2. 完整业务流程可描述。
3. 节点输入输出契约明确。
4. Compiler 与 Executor 边界明确。
5. 发布和 API 调用链路闭环。

本 Review 的目标是减少开发过程中的产品和架构歧义，而不是扩展当前阶段建设范围。