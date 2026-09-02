# Evidence-Governed Runtime

## Context

Groundary 需要把用户请求转成受控执行，同时使用 `<DATA_PROVIDER>` 数据而不让原始响应、Credential 或未经验证的语义直接进入分析。固定提交记录了 R1–R6 的分层实现和有限真实验证，但该范围不等于 Production Orchestrator 或 Full-System E2E。

## Problem

如果 Intent、工具调用、数据清洗、证据验证、计算和表达混在一个流程中，系统很难回答：数据从哪里来、字段语义是否成立、失败影响哪个分支，以及某个结论是否越过证据边界。

## Constraints

- LLM 可以解释请求，但不拥有事实、工具或动作权威。
- 真实调用需要显式授权、allowlist、预算、零隐式 fallback 和安全恢复。
- 原始供应商响应不能进入 Evidence、Trace、日志或 LLM。
- `MISSING != ZERO`、`ESTIMATED != OBSERVED`，部分能力不能升级为完整覆盖。
- 产品标识只能使用 `<PRODUCT_ID>`。

## Decision

把 Runtime 拆成职责明确的 R1–R6：执行治理、Intent、Evidence Planning、Capability/Tool 与 Collection、Evidence Validation、Deterministic Calculation。所有外部结果先经过安全封装与语义验证，之后才允许计算或受控表达。

## Implementation

R1 建立 execution context、预算、Trace 和失败边界；R2 用有限规则生成 Intent；R3 生成 Evidence Requirements；R4 通过 registry/binding 选择 allowlisted tool 并经 Gateway 采集 Shared Evidence；R5 输出可消费状态和 allowed/blocked uses；R6 对带 provenance 的 typed input 执行版本化公式。外部调用的终态恢复、usage 和 failure taxonomy 与业务结果分开记录。

## Verification evidence

固定提交中的项目上下文记录 R1–R6 在批准范围内通过本地验证，R4/R5 有受控真实 success-path 证据；相关测试覆盖 runtime foundation、intent、evidence planning、collection、validation 和 calculation。证据同时明确：partial coverage 不是 autonomous routing，受控真实验证不是生产运行，未支持语义保持 blocked/unknown。

## What was project-specific

R1–R6 名称、Groundary evidence taxonomy、`<DATA_PROVIDER>` tool bindings、受控真实操作器和 `<PRODUCT_ID>` 领域输入均为项目专属。具体 CURRENT 状态和运行配置只由 Groundary 项目文档与源码维护。

## Reusable principle

先分离 authority，再分离组件：LLM 负责受控解释，Runtime 负责预算和流程，Provider boundary 负责外部协议，Validator 负责可消费性，确定性代码负责计算。系统声称的能力强度不得超过最弱证据链。

## Limitations

本案例只概括固定提交中的受控实现与验证，不证明 Production Orchestrator、完整能力覆盖、持久 execution store、推荐引擎或 Full-System E2E。真实失败路径的覆盖也不能从一次 success-path 推断。

## Follow-up

任何生产化、完整 observability、持久化或推荐能力均需新的项目证据和独立核验；在此之前属于 `PLANNED` / `UNVERIFIED`，本案例不声明其已实现。

## Provenance

- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 3 — `backend/runtime/executionRuntime.js`; Source 4 — `backend/runtime/orchestrator/collectionRuntime.js`; Source 5 — `backend/runtime/validation/evidenceValidator.js`; Source 6 — `backend/runtime/calculation/calculationRuntime.js`; Source 7 — `backend/tests/runtime-foundation.test.js`; Source 8 — `backend/tests/orchestrator-collection-runtime.test.js`; Source 9 — `backend/tests/evidence-validation-runtime.test.js`; Source 10 — `backend/tests/calculation-runtime.test.js`
- Source commit: Sources 1–10 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Stage 6 Runtime（approved facts）`, `Formal Runtime R1–R5 checkpoint`, `R6 P0 Deterministic Calculation Runtime Closeout`; Source 2 — fixed-commit lines 502-568; Sources 3–6 — R1/R4/R5/R6 implementations; Sources 7–10 — corresponding runtime tests
- First practiced: 2026-08-24
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all listed sources. Source 2 uses line numbers computed from the fixed commit to preserve traceability without restoring the data-provider heading. Project source code and `docs/AI_PROJECT_CONTEXT.md` are factual authorities; this case is a sanitized knowledge extraction.
