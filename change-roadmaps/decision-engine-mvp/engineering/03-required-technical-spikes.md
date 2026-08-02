# Required Technical Spikes

## 0. Purpose

这些 Spike 用于验证会影响多个 Change 或造成高返工风险的技术假设。Spike 不是功能 Change，不产生面向用户的 Capability，也不直接替代后续 OpenSpec design。

每个 Spike 必须输出：

- 结论；
- 可复现原型或 fixture；
- 失败边界；
- 推荐方案与被放弃方案；
- 对 Global Engineering Baseline、Framework 和 Handoff 的影响；
- 是否解除对应 Change 的 propose 阻塞。

---

## SPK-01 — Zen-derived Engine Model Fit

### Blocks

- CHG-04
- 间接影响 CHG-09、CHG-12、CHG-15

### Questions

1. 现有 Go 引擎如何表达 Request、Decision Table、Response 和稳定节点映射？
2. 能否返回逐节点状态、命中行和判定证据？
3. Condition 多端口和无 Merge 图如何映射？
4. 产品模型中的 namespaceKey、输出 Schema 和错误策略放在引擎模型还是外围 runtime？
5. 编译产物能否稳定序列化和 hash？
6. 引擎错误如何映射到 GE-08？

### Prototype

- 固定三节点模型。
- 固定六节点模型的空适配器版本。
- Decision Table 命中、无命中和类型错误 fixture。
- 节点 ID/namespace 映射。
- Artifact 序列化和重复编译 hash。

### Success Criteria

- 三节点模型可以执行并生成可解释结果。
- 相同输入模型重复编译得到稳定结果。
- 不需要修改产品拓扑语义来迁就引擎。
- 引擎不能直接覆盖的能力可以由明确外围 runtime 承担。
- 形成 compiler/runtime responsibility matrix。

### Failure Response

若引擎无法承载关键语义，必须在 CHG-04 propose 前决定：

- 建立适配层；
- 替换部分引擎能力；
- 调整技术选型；
- 不得静默降低 Decision Table 或解释性需求。

---

## SPK-02 — CEL Subset and Cross-Language Consistency

### Blocks

- CHG-03

### Questions

1. Go 端采用哪个 CEL 实现和版本？
2. TypeScript 端采用本地 parser、WASM、服务端校验还是组合？
3. 支持哪些类型、操作符、函数和时间语义？
4. missing/null 如何表现？
5. token source 如何转换为可编译 expression？
6. 类型推断如何在编辑器中增量反馈？
7. 运行预算和正则/集合复杂度如何限制？

### Prototype

- 至少 100 个 golden fixtures。
- 覆盖 bool/int/double/string/timestamp/duration/object/array/null。
- 覆盖引用改名、缺失字段、类型错误、短路、数组函数。
- TypeScript 校验和 Go 求值自动对比。
- 大表达式和恶意输入预算测试。

### Success Criteria

- 所有支持 fixture 结果一致。
- 不支持语法在两端均明确拒绝。
- 给出 `expressionLanguageVersion`。
- 给出函数白名单和复杂度限制。
- 明确前端编辑器实现方案。

### Failure Response

若 TypeScript 本地一致性成本过高，可以使用后端增量校验，但必须保留可接受编辑体验；不得建立独立第二语义。

---

## SPK-03 — Counter Atomic Read-Before-Write

### Blocks

- CHG-05

### Questions

1. 计次和分组去重分别使用何种 Redis 结构？
2. 如何原子读取历史值并记录当前请求？
3. 滑动窗口或固定桶的精度和容量如何权衡？
4. 幂等 marker 的作用域和 TTL 是什么？
5. 热点 key 如何处理？
6. Redis 故障时 Counter 节点是失败还是可配置降级？
7. 测试命名空间如何隔离和清理？

### Prototype

- 计次 5m/1h/24h。
- 分组去重。
- 并发 100/1000 请求。
- 同 request id 重放。
- 跨 tenant/flow/env 隔离。
- 热点 key。
- Redis failover/timeout。
- 测试 namespace 清理。

### Success Criteria

- 输出值严格不含当前请求。
- 当前请求对下一请求可见。
- 重放不重复计数。
- 无跨租户/环境污染。
- 达到参考容量和延迟预算。
- 给出存储增长、清理和降级方案。

---

## SPK-04 — Artifact Distribution and Atomic Activation

### Blocks

- CHG-07
- CHG-08

### Questions

1. Artifact payload 的 P0 权威存储位置是什么？
2. executor 本地缓存、共享缓存和加载源如何组合？
3. Active Pointer 如何原子切换？
4. 控制面提交后如何通知 executor？
5. 广播丢失时如何最终收敛？
6. 控制面/PostgreSQL 暂时不可用时，已有 Artifact 能否继续执行？
7. 多副本和并发发布如何处理？
8. 旧 Artifact 保留多久？

### Prototype

- 两个 executor 副本。
- 发布新 Artifact 并并发调用。
- 预热失败。
- Pointer 切换后失效消息丢失。
- PostgreSQL 不可用。
- Redis cache 不可用。
- 发布回滚。
- cache stampede。

### Success Criteria

- 指针切换具有单一权威结果。
- 无请求落到不存在或半写入 Artifact。
- 失败保留旧版本。
- 已缓存 Artifact 在控制面故障时继续服务。
- 所有副本在明确时间内收敛。
- 给出一致性时序和错误处理。

---

## SPK-05 — Function Isolation Runtime

### Blocks

- CHG-15

### Questions

1. 候选 VM 是否支持所需 ECMAScript、ESM、async/Promise？
2. 无限循环能否可靠中断？
3. 内存是否可以硬限制？
4. 多租户并发是否互相影响？
5. 白名单模块如何提供？
6. 源码行列和受控堆栈是否可用？
7. 内嵌 executor 与独立 sandbox service 的成本和安全差异是什么？
8. 崩溃是否可能终止 executor 进程？

### Prototype

- 正常 object transformation。
- ESM import。
- async/Promise。
- 无限循环。
- 内存爆炸。
- 深递归。
- 超大输出。
- 访问 fetch/process/fs/env/dynamic import。
- 100 并发租户。
- VM crash/exception。

### Success Criteria

- 禁止能力不可访问。
- 超时和输出限制可靠。
- 内存/崩溃不会拖垮其他租户。
- 可以返回稳定错误位置。
- 明确选择内嵌或独立服务。
- 给出 Function 资源预算。

### Failure Response

允许：

- MVP Function 降级为同步受控子集；
- 使用独立隔离服务；
- 将 CHG-15 移出 MVP。

不允许未经验证直接把任意用户 JS 放入 executor 主进程。

---

## SPK-06 — Durable Execution Log Pipeline

### Blocks

- CHG-16
- CHG-08 只需采用兼容的最小接口，不要求完整结果

### Questions

1. executor 到 durable boundary 使用什么机制？
2. ClickHouse 不可用时缓存多少、多久？
3. 进程重启如何恢复？
4. 至少一次投递如何处理重复？
5. 数据脱敏和截断在哪里执行？
6. 队列耗尽时如何降级和告警？
7. schema 演进和 ClickHouse migration 如何处理？

### Prototype

- 正常批量写。
- ClickHouse 停机。
- durable queue 停机。
- executor 重启。
- 重复投递。
- 超大节点日志。
- 敏感字段 redaction。
- schema version upgrade。

### Success Criteria

- 决策响应不被日志依赖阻塞。
- durable boundary 后可恢复。
- 重复可去重或查询容忍。
- 敏感数据不会进入落盘日志。
- 积压、丢弃风险和恢复可观测。
- 给出容量和保留策略。

---

## Spike Completion Order

```text
SPK-01、SPK-02 可以与 CHG-01/02 前期并行，但必须在 CHG-03/04 propose 前完成。
SPK-03 必须在 CHG-05 propose 前完成。
SPK-04 必须在 CHG-07 propose 前完成。
SPK-05 可以在 P0 之后执行，但必须在 CHG-15 propose 前完成。
SPK-06 可以在 P0 日志接口建立后执行，必须在 CHG-16 propose 前完成。
```

## Review Rule

Spike 结论若改变用户行为，应回流 PRD；若改变系统边界，应回流 Architect；若改变 Change 数量或依赖，应回流 Framework；若只确定实现方案，则更新 Global Engineering Baseline 或对应 Change 的 local design input。
