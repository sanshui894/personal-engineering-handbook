# Learning Knowledge Base

本文件解释从实践中形成的可复用理解：是什么、为什么、概念差异、何时使用、常见误区和适用边界。简短逐词定义留在 [Technical Glossary](../reference/TECH_GLOSSARY.md)，具体项目演进留在 [Groundary Case Studies](../case-studies/groundary/README.md)。本文不维护 Groundary 当前运行状态。

## Credential、所有权与使用模式

### Provenance

- Source scope: 本来源块覆盖“Credential、所有权与使用模式”下的“是什么”“关键区别”“何时使用”“常见误区”“从 Groundary 得到的实践认识”全部内容。
- Knowledge type: GENERAL
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 2 — `docs/AI_PROJECT_CONTEXT.md`; Source 3 — `backend/services/credentials/credentialResolver.js`; Source 4 — `backend/storage/credentialStorage.js`; Source 5 — `backend/tests/credential-foundation.test.js`
- Source commit: Source 1 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 4 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 5 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Credential` through `System / User × MCP / LLM 四类 Credential`, `Managed Mode`, `BYOK（Bring Your Own Key）`; Source 2 — `Identity & Security Model`, `Data Ownership Model`; Source 3 — credential resolution implementation; Source 4 — ownership-bound persistence; Source 5 — credential foundation tests
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all listed documentation, implementation, and tests. Credential values, storage files, and environment names are intentionally excluded.

### 是什么

Credential 是系统向另一系统证明身份或取得访问权的敏感材料。工程问题不只是“保存一个值”，而是同时确定：谁拥有它、用于哪类 Provider、哪次执行可以使用、在哪里解密、如何撤销，以及哪些 metadata 可以安全展示。

Credential 的两个独立维度是所有者和用途：

| 维度 | 分类 | 含义 |
|---|---|---|
| 所有者 | System Credential | 平台拥有，由受控基础设施管理。 |
| 所有者 | User Credential | 明确绑定单个用户，所有操作和解析都要验证资源所有权。 |
| 用途 | Data / Tool Credential | 用于 `<DATA_PROVIDER>` 一类数据或工具能力。 |
| 用途 | Model Credential | 用于 `<LLM_PROVIDER>` 一类模型能力。 |

Managed 表示平台显式选择 System Credential 和默认 Policy；BYOK 表示用户显式选择自己的 User Credential。保存了 User Credential 不应自动切换模式。

### 关键区别

- Authentication 确认当前用户是谁；resource-level Authorization 还要确认目标 Credential 是否属于该用户。
- Provider 提供能力；Credential 允许访问该能力；Resolver 决定本次执行使用哪个安全上下文。
- System Credential 可以服务平台管理模式；User Credential 必须保持用户级隔离，不能进入跨用户轮换池。
- Secret masking 只用于展示少量不可恢复 metadata，不是加密，也不能作为安全存储。
- BYOK 失败不应静默回退 Managed，否则可能隐藏错误、越过费用选择或消耗平台额度。

### 何时使用

- 面向低配置门槛的默认体验，可选择 Managed，但必须有额度、用途和平台 Policy 边界。
- 用户明确拥有外部账号并主动选择时，可提供 BYOK；解析过程必须基于可信用户身份。
- 当凭据更新或删除的即时生效比性能更重要时，可每次重新验证 ownership 并构造执行上下文；缓存失效策略应作为独立设计问题。

### 常见误区

- 只校验登录状态或资源 ID，没有在查询和 mutation 中同时绑定可信用户 ID。
- 把 User Credential 放入全局可变 Key Pool，造成跨用户轮换或统计污染。
- 把完整 secret、密文材料或认证上下文返回前端。
- 把 UI 选择当作可信授权；真正的模式和所有权必须在服务端解析。
- 认为“已配置”就等于“已授权这次真实调用”。

### 从 Groundary 得到的实践认识

Groundary 的实践把 Credential Storage、Credential Resolver 和 Provider Context 分开：Storage 管理密文和 ownership，Resolver 根据可信身份与显式模式产生选择，Provider 只执行已经选定的上下文。这个分层降低了 Route 接触明文、跨用户共享可变状态和隐式 fallback 的风险。

## Hash、Encryption 与 Authenticated Encryption

### Provenance

- Source scope: 本来源块覆盖“Hash、Encryption 与 Authenticated Encryption”下的“是什么与为什么”“AES-256-GCM 组成”“何时使用”“常见误区”“从 Groundary 得到的实践认识”全部内容。
- Knowledge type: GENERAL
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 2 — `backend/services/credentials/credentialCrypto.js`; Source 3 — `backend/tests/credential-foundation.test.js`
- Source commit: Source 1 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Hash vs Encryption` through `Encryption at Rest`; Source 2 — credential crypto implementation; Source 3 — encryption round-trip, randomness, tamper, and missing-key tests
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: This shared block also covers password hashing, TLS, disk encryption and key governance, so NIST SP 800-38D would be too narrow to verify the whole unit. This is implementation-derived learning, not a cryptographic specification. No key, ciphertext, IV, tag, or credential value is included.

### 是什么与为什么

Hash 是单向转换，适合验证“这次输入是否与先前设置匹配”；Encryption 是使用密钥进行的可逆保护，适合业务必须恢复原文再调用外部系统的场景。判断标准不是数据是否敏感，而是系统是否必须恢复原文。

Encryption at Rest 让静态存储中的敏感字段以密文存在，降低单独取得数据库文件时的直接暴露风险。它不能替代传输加密、访问控制、日志卫生、密钥保护和备份治理。

### AES-256-GCM 组成

AES-256-GCM 是 authenticated encryption：

- 256-bit Master Encryption Key 是长期受控的加解密根密钥，应与数据库分开管理。
- IV / Nonce 是每次加密的唯一输入；通常不要求保密，但同一 key 下不得复用。
- Ciphertext 隐藏原文。
- Authentication Tag 验证密文和相关上下文未被篡改；验证失败必须 fail closed。

相同 plaintext 重复加密应因随机 IV 产生不同密文。Master Key 丢失会使既有密文无法恢复，因此密钥备份和轮换需要单独的受控方案。

### 何时使用

- 密码验证等无需恢复原文的场景使用适合该用途的 password hashing 方案。
- 外部 API Credential 需要恢复后发送给 Provider，使用经过审查的 authenticated encryption，并把解密限制在服务边界。
- 数据库字段加密用于保护特定敏感列；磁盘加密和 TLS 分别保护设备与传输层，职责不同。

### 常见误区

- 把 Base64 编码当作加密。
- 只保存 API Credential 的 Hash，随后却需要恢复它进行调用。
- 固定或复用 GCM nonce。
- 忽略 Authentication Tag，或验证失败后继续处理。
- 把 Master Key 与数据库、源码或日志保存在同一暴露边界。
- 认为有 Encryption at Rest 就不再需要用户隔离和最小权限。

### 从 Groundary 得到的实践认识

Groundary 的 Credential 实现把随机 IV、密文和认证 Tag 作为记录材料，把根密钥留在存储之外，并用 round-trip、随机性、篡改和错误 key 测试验证 fail-closed 行为。可复用原则是：加密设计要由可测试的失败边界支撑，不能只在 schema 中增加几个字段。

## Provider、Adapter、Resolver 与可测试依赖

### Provenance

- Source scope: 本来源块覆盖“Provider、Adapter、Resolver 与可测试依赖”下的全部正文和模式表。
- Knowledge type: GENERAL
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 2 — `backend/providers/llm/providerContract.js`; Source 3 — `backend/providers/llm/mockLlmProvider.js`; Source 4 — `backend/services/credentials/credentialResolver.js`; Source 5 — `backend/tests/llm-provider-foundation.test.js`; Source 6 — `backend/tests/llm-resolution.test.js`
- Source commit: Source 1 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 4 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 5 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 6 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Provider`, `Credential Resolver`; Sources 2–4 — provider contract, mock adapter, and resolver implementations; Sources 5–6 — provider and resolution tests
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: Derived from all listed sources. Real supplier identities and endpoint details are replaced with placeholders.

这组模式把“业务要完成什么”与“如何访问外部能力”分开：

| 模式 | 负责 | 不负责 |
|---|---|---|
| Provider | 对接数据或模型能力边界。 | 不决定用户所有权或业务推荐。 |
| Adapter | 把 `<DATA_PROVIDER>` / `<LLM_PROVIDER>` 协议归一为内部 Contract。 | 不向上暴露认证头、原始字段或流协议差异。 |
| Resolver | 根据可信 Context、task type、Policy 和可用 Adapter 形成执行计划。 | 不默认选择未知任务或静默 fallback。 |
| Dependency Injection | 从外部传入 fetch、clock、storage 或 adapter。 | 不等于插件系统。 |
| Mock | 遵循同一 Contract 但不访问真实网络。 | 不证明真实 Provider 行为。 |

为什么使用：供应商协议、模型选择、用户凭据和业务 Prompt 变化频率不同。通过 Contract 和 Adapter 隔离后，业务服务只表达 task type、messages 和 metadata；平台 Policy 决定 provider/model 参数；Adapter 处理 HTTP、stream、timeout、abort 和错误归一化。

常见误区包括：在业务层散落 Provider 判断；让未知 task 默认走某个模型；用完整文本假装 stream；把客户端断开与正常 `end` 混为一谈；Mock 偷偷读取真实配置；用错误 fallback 隐藏费用或所有权问题。

Groundary 的实践认识是：Provider Registry 可以先是一个小型依赖注入容器，不必提前扩展为动态插件系统；外部 AbortSignal 和内部 timeout 应映射为不同稳定错误；公开错误不应泄露原始供应商内容。

## SQLite Schema Migration

### Provenance

- Source scope: 本来源块覆盖“SQLite Schema Migration”全部正文和检查清单。
- Knowledge type: GENERAL
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 2 — `backend/storage/migrations/001-user-data-isolation.js`; Source 3 — `backend/storage/migrations/002-user-credentials.js`; Source 4 — `backend/tests/credential-foundation.test.js`
- Source commit: Source 1 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 4 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `SQLite Schema Migration`; Sources 2–3 — versioned migration implementations; Source 4 — migration and ownership verification tests
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: No database file or data content is copied.

Schema Migration 是把已有数据库从一个明确版本可重复地转换到下一版本。它与“应用启动时创建缺失表”不同：后者不能证明既有字段、约束、索引或历史数据已经正确转换。

为什么使用：系统演进需要在保留已有数据的同时增加所有权字段、表和约束；删除数据库或只更新全新安装 schema 会破坏历史数据和升级路径。

最低检查清单：

- 只接受明确的起始 `PRAGMA user_version`，未知版本 fail closed。
- 在迁移连接中显式启用并验证 foreign keys。
- 在事务内完成 schema、索引、数据转换和版本提升。
- 成功前执行结构、foreign key 和完整性检查。
- 验证失败回滚、重复进入和 legacy/unowned 数据边界。
- 备份用于灾难恢复，但不替代事务回滚和迁移测试。

常见误区：提前提升版本；用 `CREATE TABLE IF NOT EXISTS` 代替 schema 验证；把 App Version 与 Database Version 混为一谈；只测空数据库；迁移后没有验证多用户 SQL ownership。

## Web 部署层级与 Reverse Proxy

### Provenance

- Source scope: 本来源块覆盖“Web 部署层级与 Reverse Proxy”全部正文、链路图和验证顺序。
- Knowledge type: GENERAL
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 2 — `docs/AI_PROJECT_CONTEXT.md`; Source 3 — `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: Source 1 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Web Deployment / Web 应用部署`; Source 2 — fixed-commit lines 1221-1244; Source 3 — `Nginx 公网测试部署`
- First practiced: 2026-08-21
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: Source 2 uses line numbers computed from the fixed commit so the deployment record stays traceable without restoring the cloud-provider heading. Cloud identity, addresses, paths, service names, ports, authentication values, and database file were replaced or omitted.

Web Deployment 是把静态文件、应用进程、网络入口、反向代理、依赖和数据存储组成可验证链路。`localhost` 只代表发起访问的当前机器；应用监听、主机防火墙、云网络和公网地址属于不同层。

```text
Browser
  → Firewall
  → Nginx
      ├─ static files
      └─ /api/* and /health
           → application on 127.0.0.1:<PORT>
                → storage
```

Reverse Proxy 让浏览器面对统一入口，同时把后端路径转交内部应用。它解决路径和入口统一问题，但不会自动修复应用、认证或数据库。

验证顺序应分层：配置语法 → 代理服务状态 → 静态页面 → health → API → authentication/authorization → 受控持久化。常见误区是看到静态 HTML 就宣布部署完成，或把公网测试环境写成 Production。

Groundary 的实践认识是：静态页面成功而认证 API 不可达，可能只是 Nginx 缺少后端路径转发；只有分层检查才能定位故障。具体演进见 [Deployment Reverse Proxy Case](../case-studies/groundary/DEPLOYMENT_REVERSE_PROXY_CASE.md)。

## Evidence-first 安全边界

### Provenance

- Source scope: 本来源块覆盖“Evidence-first 安全边界”全部正文、链路图、边界列表和实践认识。
- Knowledge type: GENERAL
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 2 — `docs/AI_PROJECT_CONTEXT.md`; Source 3 — `backend/runtime/orchestrator/collectionRuntime.js`; Source 4 — `backend/runtime/validation/evidenceValidator.js`; Source 5 — `backend/tests/evidence-validation-runtime.test.js`; Source 6 — `backend/tests/orchestrator-collection-runtime.test.js`
- Source commit: Source 1 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 4 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 5 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 6 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — fixed-commit lines 502-568; Source 2 — `Stage 6 Runtime（approved facts）`, `Semantic Authority（approved facts）`; Sources 3–6 — collection, validation, and corresponding tests
- First practiced: 2026-08-24
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all listed sources. Source 1 uses line numbers computed from the fixed commit so the record stays traceable without restoring the data-provider heading. Provider identity is replaced with `<DATA_PROVIDER>` and no raw response or operational identifier is included.

Evidence-first 把“外部调用成功”拆成一组独立职责：

```text
Trusted Context / Credential Resolution
  → Tool Allowlist and Usage Guard
  → Provider Boundary
  → Sanitization
  → Semantic Normalization
  → Safe Envelope
  → Evidence Validation
  → Shared Evidence
  → Deterministic Utility / Domain Analysis
  → Policy and Conditional Recommendation
  → gated synthesis
```

关键区别：

- Raw response 不是 Evidence；Evidence 需要 source、scope、timestamp、lineage、quality 和状态。
- Sanitization 限制字段、类型、深度、大小和不可信文本位置；Semantic Normalization 再把已知 presentation 转为稳定语义。
- Validator 判断 Evidence 能否用于后续推导，不补零、不猜单位、不做市场判断。
- MISSING 不等于 ZERO；ESTIMATED 不等于 OBSERVED；Provider 字段名本身不建立语义权威。
- Capability 先于 Tool 选择；Tool 必须由 Registry / Binding 且通过 allowlist。
- Safe execution 约束失败方式，不保证 Provider 成功或业务结果完整。
- 失败 execution 也是正式 Evidence，但只保留安全错误分类、预算、恢复和可用性元数据。
- Controlled Real validation 只证明授权 slice，不等于 Production Runtime 或 Full-System E2E。
- 一次成功不能重写历史失败根因；未知原因继续保持 UNKNOWN。

常见误区包括：把 raw payload 直接交给 LLM；把字段有值当作单位和主体都已确认；用文档名义预算填充实际 usage；把 partial capability 宣称为完整覆盖；在真实执行结束后没有恢复 kill switch 和运行模式。

Groundary 的实践认识是：`VALID / PARTIAL / INVALID / FAIL_CLOSED` 都可以是诚实结果；当证据只支持展示而不支持推导时，allowed uses 与 blocked uses 应显式分开。具体演进见 [Evidence-Governed Runtime](../case-studies/groundary/EVIDENCE_GOVERNED_RUNTIME.md)。

## Agent Engineering 基础知识

### Provenance

- Source scope: 本来源块覆盖“Agent Engineering 基础知识”下从“核心认识”到“评估、Golden Case 与边界”全部内容。
- Knowledge type: GENERAL
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 2 — `docs/AI_PROJECT_CONTEXT.md`; Source 3 — `docs/AI_WORKFLOW_GOVERNANCE.md`; Source 4 — `backend/runtime/executionRuntime.js`; Source 5 — `backend/runtime/question-decomposition/questionDecompositionContractV0_3.js`; Source 6 — `backend/tests/runtime-foundation.test.js`
- Source commit: Source 1 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 4 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 5 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 6 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Agent Engineering 基础知识`; Source 2 — `Post-Stage-6 Research — Question Decomposition V0.3 Controlled Runtime Closeout`; Source 3 — `Governing Principles`, `Evidence Authority Chain`; Sources 4–6 — execution runtime, semantic contract, and runtime tests
- First practiced: 2026-08-24
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: Derived from all listed sources. It explains reusable Agent Engineering; the Groundary R1–R6 evolution is documented in case studies instead.

### 核心认识

Agent Engineering 是让目标驱动、会使用能力和工具的系统可靠、可控、可测试、可追溯的工程实践。它不等于 Prompt Engineering，也不等于简单把 LLM 接到 API。

```text
User Request
  → Intent
  → Tasks and Dependencies
  → Capability / Tool
  → Evidence and Validation
  → Deterministic Calculation
  → Domain Analysis
  → Context and Policy
  → Confidence / Abstention / Clarification
  → governed synthesis
```

LLM 可以解释语言、提出受控分解和组织表达，但不能自由补数据、心算关键指标、创造 Policy 或越过 Evidence authority。Platform / Runtime 保持事实、工具、预算和动作权威。

### 组件职责与区别

- Intent 描述用户真正想完成什么；Task 是为 Intent 拆出的可执行单元；Tool 是具体外部动作。
- Capability 描述系统能独立声明和测试的职责；带领域语义的 Skill 需要输入输出、Evidence 要求、失败边界和 non-responsibilities。
- Deterministic Utility 执行公式；Domain Analysis 解释领域状态；Decision Capability 组合 Finding、Context、Policy 和 Confidence。
- Orchestrator 管依赖、并行、局部失败、Evidence reuse 和 gate；不计算指标或创造判断规则。
- Conversation 承载多轮消息；Execution 是一次实际运行；Message 不自动成为 Evidence。
- Context 是当前执行可用信息；Memory 是跨步骤或时间保存并可取回信息的机制；Context Window 只是单次模型输入容量。

### 什么时候澄清、弃答或保留人类

缺少用户可补充的 Context 时提出 Clarification；证据、Policy 或 Authority 不足时 Abstain。真实外部调用、Policy activation、高影响动作和关键评估需要 Human-in-the-loop。可靠 Agent 的能力包括知道何时不能答，而不是始终输出结论。

### Static Workflow 与 Dynamic Planning

Static Workflow 路径固定，清晰且易测试；Dynamic Planning 会依据 Intent、Evidence、Context 和 Failure 动态增减、跳过或澄清 Task。设计 Contract 可早于 Runtime，但不能因文档存在就宣称 Dynamic Planning 已实现或验证。

### Observability 与状态

Execution State 至少要表达 pending、running、completed、failed、cancelled 等生命周期。Trace 应通过 request / execution correlation 记录 task、tool、usage、latency、cost 和安全错误类别，不记录 Secret 或原始响应。最终答案出错时，需要定位错误发生在 intent、tool、evidence、calculation、policy、decision 还是 synthesis。

### 评估、Golden Case 与边界

Agent Evaluation 应分别测量 evidence accuracy、calculation accuracy、tool selection、policy compliance、hallucination、abstention、latency、usage 和 cost。“输出看起来不错”不是充分证据。

Golden Case 用于发现职责和代表性行为，不自动是 Ground Truth；清洗后的固定输入和可信预期可成为 Regression Fixture。Mock / fixture 通过只能证明受控测试行为，不能升级为真实 Provider 或生产事实。具体 Groundary 工作流治理演进见 [AI Workflow Governance Case](../case-studies/groundary/AI_WORKFLOW_GOVERNANCE_CASE.md)，问题分解实践见 [Question Decomposition Case](../case-studies/groundary/QUESTION_DECOMPOSITION_CASE.md)。

## Learning Backlog

以下条目来自 Groundary 演进中发现的待学习方向。它们是问题和验证计划，不表示 Groundary 已实现、已选择或已经掌握相应能力。

### Provenance

- Source scope: 本来源块覆盖“Learning Backlog”表格中的 `TTL`、`LRU`、`Quota`、`Rate Limit`、`RAG`、`Embedding`、`Vector Database`、`Agent Memory`、`Observability` 九个条目。
- Knowledge type: LEARNING_BACKLOG
- Knowledge status: PLANNED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/LEARNING_KNOWLEDGE_BASE.md`; `docs/AI_PROJECT_CONTEXT.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `待学习知识`; `Agent Engineering 基础知识`; `Knowledge / Memory Architecture`; `Current Technical Debt / Risks`
- First practiced: UNKNOWN
- Last verified: NEVER
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: Groundary fields record only where these learning questions arose; they are not technical authority for the planned topics. Every entry is an unverified question under a planned learning program; no implementation claim is made.

| Topic | Knowledge status | UNVERIFIED 学习问题 | 计划验证方式 |
|---|---|---|---|
| TTL | PLANNED | 生命周期到期应如何定义，过期由谁判定？ | 比较缓存、Credential context 和 trace retention 的需求，建立时钟与边界测试。 |
| LRU | PLANNED | 容量受限缓存是否需要按最近使用淘汰？ | 先量化访问模式、容量和失效正确性，再比较无缓存与 LRU。 |
| Quota | PLANNED | 用户、功能、Provider 与 execution 的额度如何分层？ | 定义计量单位、原子扣减、恢复和审计场景。 |
| Rate Limit | PLANNED | 频率限制与 Quota、Retry、并发预算如何协作？ | 用突发、持续流量和显式限流错误建立测试矩阵。 |
| RAG | PLANNED | 哪类问题真正需要检索增强，而不是直接查询结构化 Evidence？ | 先定义语料权威、召回指标、引用和弃答评估。 |
| Embedding | PLANNED | 哪些内容适合向量表示，如何验证相似度的业务意义？ | 比较模型、分块和 benchmark，不把相似当成事实。 |
| Vector Database | PLANNED | 规模、过滤、更新和删除要求是否需要专用向量存储？ | 以真实容量与延迟需求比较现有存储和专用方案。 |
| Agent Memory | PLANNED | 哪些信息可以跨 execution 保存，所有权和遗忘规则是什么？ | 先分类 conversation、context、evidence 与 user memory，再做隐私威胁建模。 |
| Observability | PLANNED | 如何从基础 Trace 发展到可运营的指标、告警和成本诊断？ | 定义安全 telemetry schema、SLO、采样、保留和故障演练。 |
