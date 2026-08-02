# Decision Engine MVP Change Roadmap

## 1. Roadmap 目的

本目录是阶段 2 与阶段 3 的交付包，用于把阶段 1 已收口的 PRD 和 Architect 转换为可按 OpenSpec 推进的 Change Capability Framework。

它不包含实际 OpenSpec Change Artifacts，也不替代 `openspec/specs/`、`proposal.md`、`design.md` 或 `tasks.md`。

## 2. 文档分层

### 阶段 1：产品与架构语义基线

- `docs/2026-02-27-decision-engine-saas-design.md`
- `docs/2026-05-24-decision-engine-saas-technical-architecture.md`

解决产品行为、系统边界、关键一致性和范围问题，不按 Change 组织。

### 阶段 2：Change Capability Framework

- `01-capability-map.md`
- `02-change-framework.md`
- `03-dependency-graph.md`
- `04-coverage-matrix.md`
- `handoffs/*.md`

解决“有哪些 Capability、如何拆成 Change、按什么顺序推进、每个 Change 的语义边界是什么”。

### 阶段 3：Roadmap 全局工程对齐

- `engineering/01-global-engineering-baseline.md`
- `engineering/02-change-engineering-index.yaml`
- `engineering/03-required-technical-spikes.md`

解决“哪些工程契约必须跨 Change 统一、每个 propose 必须精准读取什么、哪些技术风险必须提前验证”。

## 3. 进入标准 OpenSpec 工作流

阶段 3 完成后，按依赖顺序对每个 Change 执行：

```text
handoff
+ engineering index 指定的全局章节
+ 当前 openspec/specs
+ 当前代码
    ↓
propose
    ↓
apply
    ↓
verify
    ↓
archive
```

当前 Change 的数据库、API、算法、文件和测试任务由该 Change 的 `design.md` 与 `tasks.md` 确定。阶段 3 不提前替代这些局部设计。

## 4. 权威关系

| 问题 | 权威来源 |
| --- | --- |
| 未来产品行为与范围 | PRD |
| 全局架构边界 | Architect |
| Change 拆分与顺序 | Change Framework |
| 当前 Change 的语义目标 | 对应 Handoff |
| 跨 Change 工程契约 | Global Engineering Baseline |
| 当前已实现行为 | `openspec/specs/` |
| 当前 Change 的最终技术方案 | Change `design.md` |
| 当前 Change 的实施步骤 | Change `tasks.md` |

发生冲突时，不应在下游文档中静默覆盖上游。必须回到相应层修订，并检查受影响的 Framework、Handoff 或 OpenSpec Change。

## 5. Handoff 使用规则

Handoff 是精简语义输入，必须：

- 独立说明 Goal、In Scope、Out of Scope、Normative Behavior、Acceptance 和依赖；
- 不要求重新读取原始 PRD 才能理解；
- 不包含数据库表、Redis 命令、DTO、类名和文件任务；
- 不生成实际 Change Artifacts；
- 在 propose 时与 Engineering Index 和当前代码共同使用。

## 6. Review 门禁

在任何 Change propose 前，至少检查：

1. 前置 Change 已归档或被明确允许并行；
2. Handoff 与当前 PRD/Architect 无冲突；
3. Engineering Index 中所有 required sections 可用；
4. blocking Spike 已完成；
5. 当前 `openspec/specs` 和代码没有推翻 Handoff 前提；
6. Change 边界没有吸收后续 Change 的范围；
7. proposal/specs/design/tasks 形成自包含实施包。
