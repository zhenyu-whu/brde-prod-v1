# Stage 3 — Roadmap-Wide Engineering Alignment

本目录不是逐 Change 的详细设计集合，而是在标准 OpenSpec 工作流开始前，对完整 Roadmap 进行一次全局工程对齐。

## 交付物

- `01-global-engineering-baseline.md`
  - 固定跨多个 Change 必须一致的工程契约。
- `02-change-engineering-index.yaml`
  - 为每个 Change 精准指定必须读取的全局章节、前置 Change、阻塞 Spike、拥有的共享契约和本地设计问题。
- `03-required-technical-spikes.md`
  - 定义必须在相应 propose 前验证的高风险技术假设。

## 与 OpenSpec design 的边界

Stage 3 不定义：

- 具体数据库表；
- 具体 REST DTO；
- 具体 Proto 字段；
- Redis 命令；
- React 组件；
- NestJS 类；
- 当前 Change 的任务列表。

这些由当前 Change 的 `design.md` 和 `tasks.md` 决定。

Stage 3 只防止不同 Change 对以下问题各自给出不兼容答案：

- canonical flow；
- 稳定节点和变量引用；
- 表达式和错误语义；
- 输出 Schema 生命周期；
- Version/Artifact/Deployment/Pointer；
- 资源指纹和凭据轮换；
- execution mode 和副作用；
- tenant/environment 状态隔离；
- API/RPC 边界；
- 审计、日志和安全基线。

## Propose 读取流程

1. 找到当前 Change Handoff。
2. 在 Engineering Index 中找到 Change 条目。
3. 确认 predecessors 已归档。
4. 确认 blocking spikes 已完成。
5. 读取 required global sections。
6. 读取当前 `openspec/specs` 和代码。
7. 生成自包含 proposal/specs/design/tasks。
8. 将本次采用的契约和局部技术决策冻结到 Change Artifacts。
