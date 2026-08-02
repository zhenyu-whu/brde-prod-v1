# Capability Map

## 1. Capability 分层原则

Capability 必须表达独立、可验证、可被其他能力依赖的系统行为。数据库表、组件、Redis Key、Proto message 和 CI job 属于实现任务，不单独作为 Capability。

本 Roadmap 将能力分成六个域：

1. 平台与领域基础
2. 决策表达与事实能力
3. 编辑验证与编译执行
4. 版本发布与外部接入
5. 资源、副作用与权限治理
6. 可运营性与完整 MVP 硬化

## 2. Capability 树

### A. 平台与领域基础

- **CAP-A1 多租户平台运行**
  - 租户上下文
  - bootstrap 身份
  - 控制面/执行面边界
  - 基础审计、错误和追踪
- **CAP-A2 决策流领域**
  - 项目分类
  - 决策流
  - 唯一 Draft
  - revision 和并发冲突
- **CAP-A3 Canonical Graph**
  - 稳定节点和端口
  - 无环单路径拓扑
  - 布局与执行语义分离
- **CAP-A4 值与表达式**
  - Fixed/Expression
  - 稳定变量 token
  - CEL 子集和跨语言一致性
- **CAP-A5 输出 Schema 生命周期**
  - 声明 Schema
  - 推断 Schema
  - VALID/STALE/MISSING/CONFLICTED

### B. 决策表达与事实能力

- **CAP-B1 请求契约**
- **CAP-B2 决策表**
- **CAP-B3 单一与多返回响应契约**
- **CAP-B4 系统画像查询**
- **CAP-B5 环境名单查询**
- **CAP-B6 时间窗口 Counter**
- **CAP-B7 派生变量**
- **CAP-B8 条件分支**
- **CAP-B9 HTTP 查询**
- **CAP-B10 通知提交**
- **CAP-B11 名单状态变更**
- **CAP-B12 受控 Function**

### C. 编辑、验证与执行

- **CAP-C1 画布与三栏编辑**
- **CAP-C2 静态问题门禁**
- **CAP-C3 执行到节点**
- **CAP-C4 Draft 整流测试**
- **CAP-C5 测试状态隔离**
- **CAP-C6 产品模型编译**
- **CAP-C7 Artifact 加载与执行**
- **CAP-C8 可解释节点结果**

### D. 版本、发布与接入

- **CAP-D1 不可变 Flow Version**
- **CAP-D2 环境 Compiled Artifact**
- **CAP-D3 Dev Active Build**
- **CAP-D4 Test 自动审批与手动发布**
- **CAP-D5 外部执行 API**
- **CAP-D6 Prod 人工审批与手动发布**
- **CAP-D7 Deployment 失败恢复**
- **CAP-D8 回滚**

### E. 资源、安全与治理

- **CAP-E1 内置画像源目录**
- **CAP-E2 环境名单资源**
- **CAP-E3 租户成员与角色**
- **CAP-E4 高风险能力**
- **CAP-E5 环境 API Key 生命周期**
- **CAP-E6 HTTP 连接器**
- **CAP-E7 通道连接器**
- **CAP-E8 凭据对象和版本**
- **CAP-E9 资源保护与指纹**
- **CAP-E10 外联与副作用安全**
- **CAP-E11 多租户和环境隔离**

### F. 可运营性与质量

- **CAP-F1 控制面审计**
- **CAP-F2 结构化执行日志**
- **CAP-F3 指标与追踪**
- **CAP-F4 限流和配额**
- **CAP-F5 缓存与依赖故障降级**
- **CAP-F6 备份恢复**
- **CAP-F7 性能、安全和故障准入**

## 3. Capability 到 Change 的主归属

| Capability | 主 Change |
| --- | --- |
| CAP-A1 | CHG-01 |
| CAP-A2、CAP-A3、CAP-C1 | CHG-02 |
| CAP-A4、CAP-A5 | CHG-03 |
| CAP-B1、CAP-B2、CAP-B3 单一结构、CAP-C6 基础 | CHG-04 |
| CAP-B4、CAP-B5、CAP-B6、CAP-E1、CAP-E2 | CHG-05 |
| CAP-C2、CAP-C3、CAP-C4、CAP-C5、CAP-C8 | CHG-06 |
| CAP-D1、CAP-D2、CAP-D3、CAP-C6/C7 完整 | CHG-07 |
| CAP-D4、CAP-D5 | CHG-08 |
| CAP-B7、CAP-B8、CAP-B3 多返回扩展 | CHG-09 |
| CAP-E3、CAP-E4、CAP-E5 | CHG-10 |
| CAP-E6、CAP-E7、CAP-E8、CAP-E9 | CHG-11 |
| CAP-B9、CAP-B10、CAP-E10 外联部分 | CHG-12 |
| CAP-B11、CAP-E10 名单部分 | CHG-13 |
| CAP-D6、CAP-D7、CAP-D8 | CHG-14 |
| CAP-B12 | CHG-15 |
| CAP-F1～CAP-F7 | CHG-16 |

## 4. P0 Capability 闭环

```text
多租户平台
  -> 决策流 Draft
  -> 表达式与变量
  -> Request / Decision Table / Response
  -> Profile / List(query) / Counter
  -> 静态校验与 Draft 测试
  -> Flow Version / Dev Artifact
  -> Test 申请 / 发布 / 外部 API
```

该闭环保证 P0 不是静态原型，而是可连续调用、Counter 可累积、可发布到 Test 的真实垂直切片。

## 5. 完整 MVP 扩展

P0 后，Roadmap 依次增加：

1. 分支和多返回响应；
2. 正式成员、角色、高风险能力与 Key 生命周期；
3. 连接器、凭据和资源治理；
4. HTTP/Notify 与名单写副作用；
5. Prod 治理；
6. Function；
7. 运营可靠性和发布准入。

这些能力复用 P0 已建立的 graph、expression、test、version、artifact、deployment 和 isolation 契约，不重新定义基础模型。
