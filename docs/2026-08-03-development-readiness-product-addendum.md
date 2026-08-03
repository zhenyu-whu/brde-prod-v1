# 产品开发就绪补充（PRD Addendum）

本文作为现有 PRD 的开发阶段补充，不改变产品范围，仅补充工程实现所需的最小业务定义。

## 1. 核心对象模型

```
Decision Flow
  ├── Draft
  └── Version
        └── Artifact
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

## 2. 核心状态

### Version

```
Draft
  -> Created
  -> Testing
  -> Released(Test)
  -> Released(Prod)
  -> Rollback
```

### Approval

```
Pending
  -> Approved
  -> Rejected
  -> Cancelled
```

## 3. 核心用户流程

### 策略创建

创建决策流 -> 配置节点 -> 测试 -> 生成版本 -> 发布环境 -> API 调用。

### 策略变更

修改 Draft -> 生成新版本 -> 查看 Diff -> 发布 -> 保留历史版本。

## 4. 节点统一模型

所有节点均包含：

- input
- output
- config
- validation
- runtime

节点设计需保证输入输出契约明确。

## 5. 测试语义

- 节点测试：验证单节点配置。
- 流程测试：验证完整 Draft 执行。
- 环境运行：执行已发布版本。

## 6. 功能验收标准

决策流功能完成标准：

- 可以创建。
- 可以编辑。
- 可以测试。
- 可以生成版本。
- 可以发布。
- API 可以调用并返回结果。
