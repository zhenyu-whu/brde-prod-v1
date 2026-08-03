# 架构开发就绪补充（Architect Addendum）

本文作为现有技术架构文档补充，目标是支持业务功能开发，不引入生产级 SaaS 治理复杂度。

## 1. 业务领域划分

- Decision Flow Domain：流程、节点、Draft。
- Version Domain：版本、Artifact。
- Release Domain：环境发布。
- Resource Domain：画像、名单、连接器。
- Execution Domain：运行执行。
- Identity Domain：用户权限。
- Audit Domain：操作记录。

## 2. 数据模型最小定义

核心对象：

- flow
- draft
- version
- artifact
- release
- resource
- execution

示例：

Version:

```
id
flow_id
status
artifact_id
created_by
created_at
```

## 3. Compiler 边界

输入：

- Flow JSON
- Node Config
- Resource Reference

输出：

- Executable Artifact

Compiler 负责：

- 配置校验
- 模型转换
- 产物生成

Compiler 不负责执行。

## 4. 执行流程

线上执行：

```
API Request
 -> Executor
 -> Load Artifact
 -> Execute Nodes
 -> Generate Trace
 -> Response
```

测试执行：

```
Console
 -> Control API
 -> Compiler
 -> Executor
```

## 5. API 契约原则

执行 API 最小约定：

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

错误统一包含：

- code
- message

## 6. 后续阶段优化边界

以下不作为当前开发阻塞：

- 高可用
- 灾备
- KMS/HSM
- 企业 SSO
- 高级安全治理
- 大规模性能优化
