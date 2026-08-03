# 决策引擎产品开发补充规范

> 本文不是评审意见，而是对 PRD 的开发实现补充。保持原 PRD 产品范围不变，仅补充工程实现所需的最小产品定义。

## 1. 核心对象模型

Decision Flow 是用户编排和管理的业务主体。

```
Decision Flow
 ├── Draft
 └── Version
      └── Artifact
           └── Release
```

Resource 包括 Profile Source、List、Connector、Credential。

Execution 包含 Request、Trace、Node Execution Result、Response。

## 2. 核心状态

Version:

```
Draft -> Created -> Testing -> Released(Test) -> Released(Prod) -> Rolled Back
```

Release:

```
Created -> Pending -> Approved -> Published -> Failed
```

Approval:

```
Pending -> Approved -> Rejected -> Cancelled
```

## 3. 用户核心流程

```
创建决策流
 -> 添加节点
 -> 配置节点
 -> 执行测试
 -> 生成版本
 -> 发布环境
 -> API 调用
```

## 4. 节点统一规范

所有节点具备：id、type、name、input、output、config、validation、runtime。

## 5. 测试语义

| 类型 | 目标 |
| --- | --- |
| 节点测试 | 验证单节点配置和输出 |
| 流程测试 | 验证当前 Draft 完整执行 |
| 环境执行 | 验证已发布版本运行 |

## 6. 产品验收标准

- 创建决策流
- 编辑节点
- 执行测试
- 生成版本
- 发布环境
- API 调用成功
- 查询执行结果

## 7. 当前阶段边界

不要求企业级权限、高可用、灾备、高级安全治理和商业化运营能力。
