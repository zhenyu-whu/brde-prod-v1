# 决策引擎产品开发补充规范

> 本文不是评审意见，而是对 PRD 的开发实现补充。保持原 PRD 产品范围不变，仅补充工程实现所需的最小产品定义。

## 1. 核心对象模型

### Decision Flow

决策流是用户编排和管理的业务主体。

- 一个决策流拥有一个当前 Draft。
- 一个决策流可以生成多个不可变 Version。
- Version 通过 Release 在环境中生效。

关系：

```
Decision Flow
 ├── Draft
 └── Version
      └── Artifact
           └── Release
```

### Resource

资源包括：

- Profile Source
- List
- Connector
- Credential

资源由决策流引用，但不属于某个版本内部编辑对象。

### Execution

一次执行包含：

- Request
- Trace
- Node Execution Result
- Response

## 2. 核心状态

### Version 状态

```
Draft
 -> Created
 -> Testing
 -> Released(Test)
 -> Released(Prod)
 -> Rolled Back
```

### Release 状态

```
Created
 -> Pending
 -> Approved
 -> Published
 -> Failed
```

### Approval 状态

```
Pending
 -> Approved
 -> Rejected
 -> Cancelled
```

## 3. 用户核心流程

### 创建策略

```
创建决策流
 -> 添加节点
 -> 配置节点
 -> 执行测试
 -> 修复问题
 -> 生成版本
 -> 发布环境
 -> API 调用
```

### 策略变更

```
修改 Draft
 -> 生成新 Version
 -> 查看 Diff
 -> 发布
 -> 保留历史版本
```

## 4. 节点统一规范

所有节点必须具备：

```
Node
 ├── id
 ├── type
 ├── name
 ├── input
 ├── output
 ├── config
 ├── validation
 └── runtime
```

## 5. 测试语义

|类型|目标|
|-|-|
|节点测试|验证单节点配置和输出|
|流程测试|验证当前 Draft 完整执行|
|环境执行|验证已发布版本运行|

## 6. 产品验收标准

完整功能需要满足：

- 创建决策流
- 编辑节点
- 执行测试
- 生成版本
- 发布环境
- API 调用成功
- 查询执行结果

## 7. 非目标

当前阶段不要求：

- 企业级权限模型
- 高可用架构
- 灾备体系
- 高级安全治理
- 商业化运营能力
