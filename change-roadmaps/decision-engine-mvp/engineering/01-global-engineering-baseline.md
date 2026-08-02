# Global Engineering Baseline

## 0. Purpose and Authority

- Baseline ID: `BRDE-GE-BASELINE-1`
- Version: 2026-08-02
- Scope: `change-roadmaps/decision-engine-mvp`
- Input: Stage 1 PRD/Architect and all Stage 2 Handoffs
- Consumer: OpenSpec propose for CHG-01 through CHG-16

本文只固定跨多个 Change 必须一致的工程契约。某个 Change 内部的表结构、DTO、Redis 算法、UI 组件、文件组织和任务拆分仍由该 Change 的 `design.md` 决定。

规则：

1. 当前 Change 不得重新定义本文已冻结的全局契约。
2. 若实现证明全局契约不可行，必须先修订本文、Engineering Index 和受影响 Handoff。
3. 本文未固定的内容属于 Change-local design topic。
4. Blocking Spike 的结论优先于本文中的候选方案；Spike 推翻结论时必须显式升级 Baseline 版本。

---

## GE-01 Application and Ownership Boundaries

### GE-01.1 Applications

全局应用边界固定为：

- `console-web`
- `control-api`
- `control-worker`
- `executor-go`

基础设施包括 PostgreSQL、runtime Redis、cache/jobs Redis、ClickHouse、OpenTelemetry/Prometheus/Grafana 和密钥服务。

### GE-01.2 Ownership

| 对象或行为 | 权威 Owner |
| --- | --- |
| tenant、member、role、capability | control-api |
| project、flow、Draft | control-api |
| Flow Version、Release Request、Deployment、Active Pointer | control-api |
| resource definition、credential、API Key metadata | control-api |
| 编译编排和产品模型校验 | control-api |
| 实际节点求值和执行状态 | executor-go |
| List、Counter 运行状态 | executor-go/runtime Redis |
| 执行日志批处理 | executor-go + durable log path |
| 异步影响分析、预热、补偿 | control-worker |

### GE-01.3 Hot Path

外部执行请求不得经过 `control-api`。正常热路径不得每次读取 PostgreSQL。executor 使用：

1. 本地 Key/Pointer/Artifact 缓存；
2. 受控共享缓存或 Artifact 加载源；
3. singleflight 防止同一 Artifact 并发加载；
4. 失败时按已有缓存和错误策略处理。

---

## GE-02 Tenant, Actor and Environment Context

### GE-02.1 Tenant Context

所有持久化、缓存、执行、日志和异步消息必须携带 `tenant_id`。业务 repository 接口不得提供无 tenant 的通用方法。

### GE-02.2 Actor Context

控制面写操作必须携带：

- `tenant_id`
- `actor_id`
- `request_id`
- 认证会话或服务身份
- 操作来源
- 必要时的 expected revision/status

worker 派生任务必须保留原 actor 和触发事件，不得伪装成人工用户。

### GE-02.3 Environment Enum

环境固定为：

```text
DEV
TEST
PROD
```

环境来源：

- 外部执行：由 API Key 绑定。
- 控制台 Draft 测试：由受信控制面参数和 execution mode 决定。
- 发布：由 Change 状态机决定。
- 客户请求中的环境 Header 只可作为一致性校验，不可改变 Key 绑定环境。

---

## GE-03 Canonical Identity and Schema Versioning

### GE-03.1 Persistent Identity

以下对象使用服务端生成、不可变、不可复用的 opaque ID：

- tenant
- user/member
- project
- flow
- draft
- node
- edge
- resource
- credential
- flow version
- artifact
- release request
- deployment
- audit event

具体编码可以由 CHG-01 设计，但不得把显示名称、数组位置或数据库自增顺序作为跨服务身份语义。

### GE-03.2 Stable Namespace

每个节点拥有不可编辑 `namespaceKey`：

- 新节点创建时生成。
- 复制节点时生成新值。
- 节点改名、移动、重新连线时不变。
- Flow Version 和历史版本重建时保留。
- 删除后不得由另一节点复用。

### GE-03.3 Version Fields

必须区分：

- `flowModelSchemaVersion`
- `nodeConfigVersion`
- `expressionLanguageVersion`
- `outputSchemaVersion`
- `compilerVersion`
- `artifactFormatVersion`
- `api/protocolVersion`

不得使用一个模糊 `version` 字段同时表示产品版本和格式版本。

---

## GE-04 Canonical Flow, Ports and Topology

### GE-04.1 Logical Shape

Canonical Flow Model 至少表达：

```text
flow metadata
draft identity + revision
nodes[]
edges[]
request contract
shared response contract
resource logical references
editor layout metadata
```

Node 至少具有：

```text
id
namespaceKey
type
configVersion
displayName
description
config
errorPolicy
outputSchemaState
uiMetadata
```

Edge 至少具有：

```text
id
sourceNodeId
sourcePortKey
targetNodeId
targetPortKey
```

### GE-04.2 Stable Ports

Port 使用节点类型定义的稳定 `portKey`，不得使用屏幕顺序或本地数组 index 作为执行语义。

- Request：`out`
- Response：`in`
- 普通节点：`in`、`out`
- Condition：`in`、每个分支稳定 branch/port key

分支显示顺序可以变化，但 branch identity 必须稳定；顺序变化属于语义变化。

### GE-04.3 Topology

MVP 固定：

- 单 Request。
- 一个或多个 Response。
- 无环。
- 无隐式 Merge。
- 无并行。
- 普通节点至多一入一出。
- Condition 一入多出。
- 每个 Condition 分支最终到独立 Response。
- 不允许孤岛、不可达节点或悬空必需出口。

图校验必须在前端提示、控制面权威校验和编译器防御校验三层实施。

---

## GE-05 Typed Values and Expression Contract

### GE-05.1 Value Types

共享逻辑类型至少包含：

```text
null
bool
int
double
string
timestamp
duration
object
array<T>
```

MVP 不允许自由 decimal、binary、function value 或隐式 union。具体 JSON/Proto 编码由 Change 设计，但跨语言必须无歧义。

### GE-05.2 Fixed Values

Fixed 值必须保存：

- 逻辑类型
- 规范化值

不得在运行时根据字符串内容自动猜测数字、布尔或时间。

### GE-05.3 Expression Values

Expression 持久化必须包含：

- `languageVersion`
- 可编辑 source
- 稳定引用信息
- 可选静态类型结果
- 校验摘要

显示 token 不是唯一事实源。后端必须能在没有浏览器编辑器状态时重建和编译表达式。

### GE-05.4 Authority

- Go evaluator 是运行权威。
- TypeScript 负责编辑期 parser/type feedback 或调用共享校验服务。
- 两端必须执行同一 golden fixtures。
- 不允许各节点实现自己的表达式 parser。

### GE-05.5 Null and Missing

- 路径存在且值为 null：返回 null。
- 路径未观测到或不存在：基础取值返回 null。
- 对 null 进行不支持的运算：类型/运行错误。
- `has`、`isNull` 等显式函数可以区分受支持场景；具体函数集由 SPK-02 固定。

### GE-05.6 No Implicit Coercion

默认禁止：

- string -> number
- number -> bool
- arbitrary object -> string
- timestamp/duration 与普通 string 隐式互转

允许的数值 widening 必须在表达式规范中明确。

---

## GE-06 Common Node Contract

### GE-06.1 Configuration

每类节点必须具有：

- node type stable identifier
- config version
- configuration schema
- input requirements
- output contract
- validation rules
- execution semantics
- error/degradation policy support
- audit and log summary policy
- diff strategy

### GE-06.2 Node Registration

前端节点库、控制面 validator/compiler 和 executor runtime 必须通过同一 stable node type identifier 关联。某端不认识 node type/config version 时必须明确拒绝，不得跳过。

### GE-06.3 Common Execution Envelope

每个节点执行结果内部至少包含：

```text
nodeId
namespaceKey
status
startedAt
endedAt
duration
inputSummary
outputSummary
error
degradation
sideEffectSummary
resourceUsageSummary
```

并非所有字段都对下游变量可见；变量输出与诊断 envelope 必须分离。

---

## GE-07 Output Schema Lifecycle

### GE-07.1 Sources

输出 Schema 来源分为：

- `STATIC`：节点类型固定，如 List、Profile 外壳。
- `DECLARED`：用户显式声明，如 Request、Response、Decision Table 输出列。
- `INFERRED`：由成功验证样例推断，如 HTTP body、Function。
- `HYBRID`：稳定外壳 + 动态内部字段。

### GE-07.2 States

状态固定为：

```text
MISSING
VALID
STALE
CONFLICTED
```

- MISSING：尚无可用 Schema。
- VALID：Schema 与当前 Draft revision/config 一致。
- STALE：配置变化后沿用旧 Schema 仅供修复引用。
- CONFLICTED：多次观察或声明产生不可兼容类型。

### GE-07.3 Generation Gate

- 下游未引用动态字段时，可以允许 MISSING，取决于节点规则。
- 存在下游引用时，MISSING/STALE/CONFLICTED 必须阻止 Flow Version 生成。
- Flow Version 必须冻结当前有效 Schema。
- Runtime 数据与冻结 Schema 冲突时必须按节点定义返回稳定错误或受控降级，不得静默改变变量类型。

### GE-07.4 Provenance

INFERRED Schema 至少记录：

- source validation id
- source draft revision
- inferred time
- sample hash
- inference algorithm/version

---

## GE-08 Execution Status, Errors and Degradation

### GE-08.1 Node Status Enum

固定：

```text
NOT_STARTED
RUNNING
SUCCEEDED
DEGRADED
FAILED
NOT_REACHED
SKIPPED
```

### GE-08.2 Platform Error Families

外部与内部契约共享错误家族：

```text
AUTHENTICATION
AUTHORIZATION
NOT_FOUND
NOT_DEPLOYED
VALIDATION
CONFLICT
RATE_LIMITED
DEADLINE_EXCEEDED
DEPENDENCY_FAILURE
NODE_EXECUTION_FAILURE
ARTIFACT_INCOMPATIBLE
INTERNAL
```

每个错误必须包含稳定 code、human message、request id 和可选 safe details。不得返回堆栈、密文或内部网络信息。

### GE-08.3 Node Error Categories

至少：

```text
EXPRESSION_SYNTAX
EXPRESSION_TYPE
EXPRESSION_RUNTIME
REFERENCE_INVALID
RESOURCE_UNAVAILABLE
RESOURCE_INCONSISTENT
TIMEOUT
OUTPUT_SCHEMA_MISMATCH
SIDE_EFFECT_REJECTED
SANDBOX_VIOLATION
INTERNAL_NODE_ERROR
```

### GE-08.4 Degradation

`DEGRADED` 必须产生明确输出外壳和 reason，不能只在日志中记录。继续执行前，节点输出类型必须满足其公开 contract。

---

## GE-09 Flow Version, Artifact, Deployment and Pointer

### GE-09.1 Object Model

- Draft：可变，按 revision。
- Flow Version：环境无关，不可变。
- Compiled Artifact：环境相关，不可变。
- Release Request：治理流程。
- Deployment：一次切换尝试。
- Active Pointer：权威当前 Artifact。

### GE-09.2 Artifact Identity

Artifact 逻辑身份由以下输入决定：

```text
source Draft revision or Flow Version
target environment
compiler version
artifact format version
resolved behavior resources
security/runtime policy version
```

Artifact hash 使用规范化序列化后 SHA-256。

### GE-09.3 Canonicalization

全局采用 RFC 8785 JSON Canonicalization Scheme 或等价、测试可验证的 canonical JSON。若某语言库不完全支持，必须通过共享 fixture 证明等价。

### GE-09.4 Deploy Semantics

- Deployment 有唯一幂等 key。
- 同 flow/environment 同时只允许一个切换操作。
- Active Pointer 更新与 Deployment 成功状态在同一权威事务。
- cache invalidation/outbox 在提交后执行。
- 失败不得更新 Pointer。
- rollback 创建新 Deployment，不修改历史 Deployment。

---

## GE-10 Resource Resolution, Fingerprints and Credentials

### GE-10.1 Logical vs Resolved Reference

Flow Version 保存 logical resource reference。Artifact 保存目标环境 resolved binding。

### GE-10.2 Behavior Fingerprint

Resource behavior fingerprint：

```text
SHA-256(
  canonical-json(
    resource type
    + resource schema version
    + target environment
    + behavior fields after resolution
    + stable credential object reference when relevant
  )
)
```

不包含：

- display name
- description
- created/updated timestamps
- actor
- active credential version secret/content

### GE-10.3 Credential Rotation

- Credential object stable。
- Credential version immutable。
- active version switch 不改变 Flow Version。
- execution resolve 时读取 active version。
- execution log 记录 version fingerprint。
- 资源行为字段变化需要新指纹；仅密钥内容轮换不触发重新审批。

### GE-10.4 Protection

当 resource 被 Test/Prod 当前 Artifact 引用时：

- behavior fields locked。
- metadata fields 可以按设计修改。
- 行为变化通过 clone resource + Draft reference change。
- 保护判断必须来自权威引用关系，不依赖 UI 标记。

---

## GE-11 Execution Modes and Side-Effect Policy

### GE-11.1 Modes

固定 execution mode：

```text
EXTERNAL
DRAFT_NODE_TEST
DRAFT_FLOW_TEST
RESOURCE_CONNECTIVITY_TEST
```

### GE-11.2 State Behavior

| Mode | List/Counter | HTTP/Notify | 审计/日志 |
| --- | --- | --- | --- |
| EXTERNAL | 环境真实状态 | 按环境真实执行 | 执行日志 |
| DRAFT_NODE_TEST | 测试命名空间 | 默认禁止；显式真实模式例外 | 测试记录 + 必要审计 |
| DRAFT_FLOW_TEST | 测试命名空间 | 默认禁止；显式真实模式例外 | 测试记录 + 必要审计 |
| RESOURCE_CONNECTIVITY_TEST | 不适用 | 明确目标真实执行 | 强审计 |

### GE-11.3 Explicit Real Test

真实 Draft 外联必须同时具备：

- explicit target environment
- high-risk capability
- second confirmation
- connector safety pass
- audit context
- idempotency/de-duplication when supported

不得通过普通“运行测试”按钮隐式触发。

---

## GE-12 Runtime State Namespaces

### GE-12.1 List Dimensions

外部名单状态维度：

```text
tenant
environment
listResource
normalizedKey
```

测试名单增加：

```text
draft/testSession or validationExecution
```

MVP 不支持跨环境共享名单。

### GE-12.2 Counter Dimensions

外部 Counter 维度：

```text
tenant
flow
environment
counterItem
window
evaluated key dimensions
```

另有 request id/idempotency marker。

测试 Counter 增加 Draft revision 和 test namespace，且不得写入外部环境。

### GE-12.3 Cache Dimensions

Key、Pointer、Artifact cache 必须至少包含 tenant 和稳定 object identity。任何不含 tenant 的共享缓存都必须证明内容是全局公开不可变数据。

---

## GE-13 External API and Internal RPC Boundaries

### GE-13.1 External API

固定资源语义：

```http
POST /v1/flows/{flow_id}:execute
```

认证使用 Bearer environment API Key。Header：

- `X-Request-Id` 可选；平台总是返回最终 request id。
- `Idempotency-Key` 对 Counter 和支持的副作用传递。
- 客户环境 Header 不作为路由来源。

OpenAPI 的精确 body/error schema 由 CHG-08 设计，但不得改变上述语义。

### GE-13.2 API Key Format

Key 必须包含：

- 非敏感 key id/prefix，用于定位 metadata；
- 高熵 secret；
- environment 可识别前缀仅用于运维提示，不作为唯一安全判断。

数据库保存：

- key id
- HMAC/secure keyed digest of secret
- fingerprint
- tenant/environment/status/timestamps

不保存可恢复明文。

### GE-13.3 Internal RPC

Proto package 采用版本化命名，例如 `brde.executor.v1`。必须覆盖：

- execute temporary/formal artifact
- warm/load artifact
- invalidate artifact
- health/readiness

RPC 使用服务身份认证、deadline、消息大小上限和稳定 error mapping。

---

## GE-14 Concurrency, Idempotency and Transactions

### GE-14.1 Draft

- read 返回 revision/ETag。
- save 携带 expected revision。
- 冲突返回 CONFLICT 和最新 revision，不自动合并业务配置。

### GE-14.2 Version Generation

- 输入锁定明确 Draft revision。
- 生成重复请求可通过 idempotency key 返回同一结果或安全拒绝。
- 版本序号/显示编号不作为内部唯一身份。

### GE-14.3 Approval and Deployment

- 状态转换使用 compare-and-set。
- 同一申请终态不可逆。
- deployment/retry/rollback 均有独立记录。
- 同 flow/environment 串行锁。
- Outbox 至少一次，消费者幂等。

### GE-14.4 External Requests

- request id 标识一次平台执行。
- Idempotency-Key 的租户、环境、flow 和有效期作用域必须明确。
- Counter 使用该上下文避免重复记录。
- HTTP/Notify/List write 只有在节点声明支持且设计满足时才提供幂等重试；不得假设所有外部系统幂等。

---

## GE-15 Audit, Execution Logs and Observability

### GE-15.1 Audit Transaction

关键控制面写入：

```text
business mutation
+ minimal audit event
+ outbox event
```

在同一 PostgreSQL 事务中提交。富化失败不得删除最小事件。

### GE-15.2 Audit Event Minimum

```text
auditEventId
tenantId
actorId/serviceIdentity
requestId
action
objectType/objectId
environment when relevant
result
occurredAt
safe summary/fingerprint
```

### GE-15.3 Execution Log Reliability

执行日志链路必须有 durable boundary。允许：

- executor -> durable stream/queue -> ClickHouse
- executor local durable buffer + shipper -> ClickHouse

不允许只有内存队列且进程退出即永久丢失。至少一次投递要求 event id 和去重/容忍重复设计。

### GE-15.4 Trace and Metrics

- request id 与 trace id 可以不同但必须互相关联。
- Prometheus label 不使用 request id、flow display name、raw user id 等无限高基数字段。
- 失败、超时、发布、回滚、日志积压、Key 撤销延迟必须有指标。

---

## GE-16 Security Baseline

### GE-16.1 Authorization

授权决策由 control-api 权威执行。前端隐藏按钮不是安全控制。

高风险能力 stable identifiers 至少包括：

```text
FUNCTION_EDIT
CREDENTIAL_MANAGE
CONNECTOR_MANAGE
LIST_DATA_MANAGE
API_KEY_MANAGE
ACCESS_MANAGE
REAL_EXTERNAL_TEST
```

### GE-16.2 Secrets

- API Key secret 只显示一次。
- Credential secret 永不回显。
- 生产 envelope encryption 使用 KMS。
- 日志、trace、error、audit 均进行 secret scanning/redaction。

### GE-16.3 Egress

必须在实际网络调用前和 redirect 后验证：

- scheme
- host/IP class
- DNS result
- port
- private/metadata restrictions
- headers
- body/response size
- timeout
- redirect count
- decompression limits

### GE-16.4 Tenant Isolation

- PostgreSQL unique/foreign constraints 考虑 tenant。
- repository 强制 tenant。
- Redis key builder 强制 tenant。
- ClickHouse 写入和查询强制 tenant。
- cache entry identity 强制 tenant。
- 自动化跨租户测试是每个涉及新存储/缓存 Change 的验收要求。

---

## GE-17 Compatibility and Shared Fixtures

### GE-17.1 Contract Repository

共享契约必须有单一仓库位置，供 TypeScript 与 Go 生成或消费：

- Proto
- canonical flow fixtures
- expression fixtures
- artifact fixtures
- error/status enums
- node type identifiers

具体目录由 CHG-01 design 决定。

### GE-17.2 Compatibility

- Proto 使用 breaking-change check。
- OpenAPI 使用 schema diff。
- Node config version 增加时提供 migration 或明确 unsupported。
- Artifact format 不兼容时 executor 拒绝并返回 ARTIFACT_INCOMPATIBLE。
- 编译器不得静默重写历史 Flow Version。

### GE-17.3 Golden Fixtures

至少覆盖：

- graph topology
- stable namespace references
- expression success/error/null
- Decision Table hit/no-hit
- Counter idempotency
- resource fingerprint canonicalization
- artifact hash
- status/error mapping

---

## GE-18 Global Non-Functional Budgets

- 请求/响应各 256 KiB 默认上限。
- 单 Flow 100 节点。
- Decision Table 1000 行。
- 外部执行默认 deadline 1 秒。
- 六节点平台额外开销 p95 50ms 目标。
- 参考 P0 链路 p95 200ms 目标。
- executor SLO 99.9%。
- 控制面 RPO 5 分钟、RTO 60 分钟目标。
- Key 紧急撤销传播上限必须由 CHG-10 明确并测试。
- Function 资源预算由 SPK-05 和 CHG-15 固定，不得无限制。

每个 Change 可以使用更严格预算，不得未经 Framework/PRD 修订放宽全局上限。

---

## GE-19 Change-Local Design Topics

以下内容默认不在全局 Baseline 固定，由相应 propose 决定：

- Prisma 表和索引的精确结构。
- UUID/ULID 的具体库和序列化，只要满足 opaque/stable 语义。
- 前端状态管理库。
- REST DTO 字段和分页形式。
- Redis Counter 使用 ZSET、时间桶或 Lua/Function 的具体实现。
- Artifact payload 存 PostgreSQL、对象存储或组合的 P0 实现。
- BullMQ queue 名称和 worker 并发。
- ClickHouse DDL。
- Function 最终采用内嵌 VM 还是独立服务，由 Spike 决定。
- 具体代码目录和类名。

若 Change-local 决策会影响两个以上后续 Change，propose 必须判断是否先提升为 Baseline 修订。

---

## GE-20 Baseline Review Checklist

在每个 propose 开始前检查：

- required GE sections 是否读取；
- 当前 Change 是否企图重定义全局 enum、ID、状态机或环境语义；
- blocking Spike 是否关闭；
- 前置 specs/代码是否与 Baseline 一致；
- local design topics 是否足够明确；
- 是否需要修订 Framework；
- 生成的 Artifacts 是否自包含而非只引用本文。
