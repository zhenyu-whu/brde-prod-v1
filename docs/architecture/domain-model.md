# Core Domain Model

## Objects

- DecisionFlow: 用户编排和管理决策逻辑的主体。
- Draft: 当前可编辑状态。
- Version: 不可变业务版本快照。
- Artifact: 执行引擎可加载产物。
- Release: 版本在具体环境中的生效记录。
- Resource: 被决策流引用的共享能力。
- Execution: 一次运行实例及结果。

## Relationship

```
DecisionFlow
 ├── Draft
 └── Version
      └── Artifact
           └── Release
```
