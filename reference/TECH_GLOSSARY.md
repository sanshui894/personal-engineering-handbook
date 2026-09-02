# Technical Glossary

本术语表提供简明定义，不承担长篇教学、运行状态或架构权威职责。通用术语保留其 Groundary 实践来源；项目专属术语是便于学习的非权威副本，运行权威始终在 Groundary 的项目文档和源码。

## A. General Engineering Terms

### Provenance

- Source scope: 本来源块覆盖“A. General Engineering Terms”表格中除 `JWT` 外的通用词条。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/TECH_GLOSSARY.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `通用技术术语`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: 这些简明定义来自项目实践整理；共享块没有逐词条附加完整外部权威，因此保守降级。JWT 另有精确 RFC 来源块。

| Term | 简明定义 | 边界 / 易混点 |
|---|---|---|
| Authentication | 确认请求者是谁的过程。 | 不等于允许其访问某资源。 |
| Authorization | 判断已识别主体能否执行动作或访问资源。 | 必须落实到资源和动作层。 |
| Middleware | 在主处理逻辑前后执行通用职责的组件。 | 常用于认证、校验、日志和错误边界。 |
| Cookie | 浏览器按域和规则保存并随请求携带的小型数据。 | 可承载会话凭据，因此值不能进入日志或文档。 |
| Hash | 将输入单向映射为固定形式的函数。 | 适合验证，不适合需要恢复原文的场景。 |
| Encryption | 使用密钥把明文转换为可逆密文。 | 解密密钥与访问边界同样敏感。 |
| Credential | 用于证明身份或获得外部能力访问权的敏感材料。 | 与 Provider、用户登录密码和展示 metadata 分开。 |
| Schema | 数据结构、字段、约束及关系的正式定义。 | 对象存在不证明结构正确。 |
| Migration | 按版本把已有数据结构安全转换到新结构。 | 需要事务、校验、回滚和重复进入策略。 |
| CRUD | Create、Read、Update、Delete 四类资源操作。 | 仍需认证、授权、校验和审计。 |
| Provider | 提供数据、模型或外部能力的服务及其代码边界。 | 例如 `<DATA_PROVIDER>` 或 `<LLM_PROVIDER>`；不是 Credential。 |
| Adapter | 将外部协议转换为内部统一接口的组件。 | 隔离供应商差异，不决定业务策略。 |
| Contract | 组件之间约定的输入、输出、错误和语义边界。 | 代码存在不证明 Contract 已经运行验证。 |
| Resolver | 将分散输入解析为可信、标准化执行选择的组件。 | 决定选择，不承担 Provider 协议或业务结论。 |
| Policy | 可追溯、带范围和版本的判断规则。 | 与计算结果、Prompt 中无来源阈值分开。 |
| Mock | 遵循真实接口但不访问真实依赖的模拟实现。 | 用于可重复、零外部副作用测试。 |
| Stub | 只为测试提供预设返回或记录调用的最小替身。 | 通常比完整 Mock 更窄。 |
| Fallback | 主路径失败后采用另一条明确路径。 | 不应静默跨越所有权、费用或安全边界。 |
| Fail Closed | 无法确认安全或有效时拒绝继续。 | 与“尽量返回一个结果”相反。 |
| Stream | 数据分段产生和消费的方式。 | 需要处理取消、完成和部分结果。 |
| SSE | 服务端通过 HTTP 持续推送事件的文本协议。 | 连接关闭既可能是取消，也可能是正常完成。 |
| Timeout | 限制操作等待时间的预算。 | 连接、单调用和总执行可有不同预算。 |
| Retry | 在受控条件下重新尝试失败操作。 | 必须有次数、幂等性和总预算边界。 |
| API | 软件组件对外提供的可调用接口契约。 | 路由可达不等于业务成功。 |
| Port | 同一主机上区分网络服务的编号。 | 监听端口不证明防火墙或公网链路已开放。 |
| Firewall | 根据规则允许或拒绝网络流量的控制层。 | 与应用监听、反向代理是不同层。 |
| Nginx | 常用于静态服务和反向代理的 Web Server。 | 配置通过后仍需验证上游与应用。 |
| Reverse Proxy | 由统一入口接收请求并转发到内部服务。 | 静态页面成功不证明 API 转发成功。 |
| SQLite | 嵌入式、文件型关系数据库。 | 单文件部署简单，但仍需迁移、备份和并发边界。 |
| RAG | 先检索相关知识，再把检索结果用于生成的模式。 | 术语定义不表示某项目已实现。 |
| Embedding | 把内容映射为可比较数值向量的表示。 | 相似度不等于事实正确或业务相关。 |
| Vector Database | 面向向量存储和相似度检索的数据系统。 | 是否需要取决于检索规模与约束。 |

### JWT

#### Provenance

- Source scope: 本来源块只覆盖下表的 `JWT` 定义与边界。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/TECH_GLOSSARY.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `通用技术术语`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: JSON Web Token (JWT), RFC 7519 — https://www.rfc-editor.org/rfc/rfc7519
- Sensitivity: PUBLIC
- Notes: RFC 7519 is the authority for the general definition; it does not verify any Groundary authentication implementation.

| Term | 简明定义 | 边界 / 易混点 |
|---|---|---|
| JWT | 用于传递声明的紧凑、URL-safe 表示；可通过 JWS 使用数字签名或 MAC，也可通过 JWE 加密。不能仅凭 JWT 名称假设它一定已签名、已加密、可撤销或仍然有效。 | 解析成功不等于可信；使用方仍须按应用要求验证保护方式、密钥和相关声明。 |

## B. Applied AI Engineering Terms

### Provenance

- Source scope: 本来源块覆盖“B. Applied AI Engineering Terms”表格中从 `Evidence-first` 到 `Semantic Contract Engineering` 的全部词条。
- Knowledge type: GENERAL
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/TECH_GLOSSARY.md`; Source 2 — `docs/LEARNING_KNOWLEDGE_BASE.md`
- Source commit: Source 1 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`; Source 2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `通用技术术语`, `Runtime / Evidence 实践术语`, `Applied AI Engineering / Question Decomposition V0.3 术语`; Source 2 — `Evidence-first 与 Real MCP Safety Boundary`, `Agent Engineering 基础知识`
- First practiced: 2026-08-24
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: Derived by consolidating all listed Groundary terminology and learning sources; terms are reusable, but Groundary remains the origin project.

| Term | 简明定义 | 边界 / 易混点 |
|---|---|---|
| Evidence-first | 先建立、规范化和验证证据，再计算、判断或表达。 | Provider 返回不自动成为 Evidence。 |
| Evidence Validation | 检查证据结构、来源、语义、时效、范围与可消费性。 | 不负责创造业务判断。 |
| Deterministic Utility | 对同一输入按固定公式产生同一输出的工具函数。 | 数值计算与业务 Policy 分开。 |
| Conditional Recommendation | 仅在 Evidence、Context 和 Policy 条件满足时形成的建议。 | 条件不足时澄清、降级或弃答。 |
| Semantic Normalization | 将已知外部表示转换为稳定业务语义。 | 发生在安全清洗之后，不替代验证。 |
| Canonical Identity | 跨请求和外部表示仍可严格比较的规范主体身份。 | 表示形式相似不等于身份相同。 |
| Safe Envelope | 下游可消费的稳定、脱敏结果或错误结构。 | 不携带原始响应、凭据或内部堆栈。 |
| Tool Allowlist | 明确列出允许运行工具的控制。 | 未列出的工具在外部调用前拒绝。 |
| Usage Guard | 对调用数、重试、时长或费用施加预算。 | 超出预算应 fail closed。 |
| Kill Switch | 可紧急阻止新外部执行的独立开关。 | 解除开关不等于授权具体调用。 |
| Trace Correlation | 用关联标识连接同一执行中的安全事件。 | Trace 不等于无边界日志。 |
| Orchestrator | 管理任务依赖、顺序、并行、局部失败和门控的组件。 | 是控制平面，不是业务决策者。 |
| Control Plane | 管理计划、路由、状态、预算和 gate 的职责层。 | 不生成领域事实或结论。 |
| Abstention | 证据或规则不足时明确拒绝超范围回答。 | 是可靠性能力，不是普通错误。 |
| Human-in-the-loop | 在澄清、复核、批准或高影响动作中保留人类参与。 | 成熟 Agent 不等于全自动。 |
| Golden Case | 用于发现职责、边界和代表性行为的案例。 | 不自动等于 Ground Truth。 |
| Regression | 既有可信行为在变更后退化。 | 应用固定输入与明确约束检测。 |
| Evaluation-driven Development | 先建立可复现评估，再用结果驱动设计迭代。 | 不以输出观感替代指标和 rubric。 |
| Semantic Contract Engineering | 用受控词表、结构和校验规则约束 AI 输入输出语义。 | 契约定义不等于模型总能遵守。 |

## C. Project-Specific Vocabulary

本节是 **Non-authoritative educational copy**。所有词条均为 `PROJECT_SPECIFIC`；Runtime authority remains in Groundary。供应商身份只使用 `<DATA_PROVIDER>`、`<LLM_PROVIDER>` 等占位符。

### Provenance

- Source scope: 本来源块覆盖“C. Project-Specific Vocabulary”表格中从 `R1` 到 `PRODUCT_BINDING_PROJECTION_BUG` 的全部词条。
- Knowledge type: PROJECT_SPECIFIC
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/AI_PROJECT_CONTEXT.md`; `docs/TECH_GLOSSARY.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `Stage 6 Runtime（approved facts）`; `Post-Stage-6 Research — Question Decomposition V0.3 Controlled Runtime Closeout`; `Runtime / Evidence 实践术语`
- First practiced: 2026-08-26
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Non-authoritative educational copy. Runtime authority remains in Groundary project documentation and source code; this glossary must not be used to infer current runtime state.

| Term | Groundary 中的简明含义 | 权威边界 |
|---|---|---|
| R1 | Execution Runtime：执行上下文、预算、状态、Trace 与失败边界。 | 当前实现和状态以 Groundary 为准。 |
| R2 | Intent Routing：把有限自然语言输入映射为受控 Intent。 | 不代表开放式自主理解。 |
| R3 | Evidence Planning：把 Intent 转换为证据需求和计划。 | Plan 不等于已采集 Evidence。 |
| R4 | Capability / Tool Runtime：能力解析、工具绑定、受控采集与 Shared Evidence。 | Tool selection 不等于 autonomous routing。 |
| R5 | Evidence Validation：验证证据结构、语义和 allowed/blocked uses。 | 不产生业务建议。 |
| R6 | Deterministic Calculation Runtime：对有来源输入执行版本化公式。 | 不替代 Recommendation 或 Decision Runtime。 |
| CONTROLLED_REAL | 在显式授权、预算、网关和恢复边界内进行的真实外部调用。 | 不等于 Production Runtime 或普通开发命令。 |
| QUESTION_DECOMPOSITION_V0_3 | Groundary 的版本化问题分解语义契约。 | 只解释和分解请求，不授予证据、工具、建议或动作权威。 |
| R2_INTENT_V0_2 | Groundary 既有受控 Intent 模型版本。 | richer meaning 无法安全映射时应拒绝，而不是压缩语义。 |
| PARTIAL_CAPABILITY_COVERAGE | Runtime 仅覆盖已实现的 Intent / Capability 子集。 | 不隐含完整回答能力。 |
| CONTROLLED_REAL_VALIDATION_OPERATOR | 在单独授权与恢复约束下执行 scoped validation 的项目操作器。 | 不是日常命令或生产编排器。 |
| PRODUCT_BINDING_PROJECTION_BUG | 外部嵌套身份表示未按 canonical identity 投影导致的历史缺陷类别。 | 具体修复与验证事实以 Groundary 源码和项目上下文为准。 |
