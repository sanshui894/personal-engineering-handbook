# Groundary Case Study

## Repository context

### Provenance

- Source scope: 本来源块仅覆盖“Repository context”下的项目名称、仓库和定位两段。
- Knowledge type: CASE_STUDY
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `README.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `1. What is Groundary`
- First practiced: 2026-09-02
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: This overview is fixed-commit context, not a live runtime-status source. Project documentation and source code remain authoritative.

Groundary 原名 MH-ASIN，当前项目仓库为 <https://github.com/sanshui894/Groundary>。

项目定位：**Evidence-Governed Decision System**。

### Case-directory boundary provenance

- Source scope: 本来源块仅覆盖下方关于案例目录边界与禁止复制内容的两段。
- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `docs/AI_WORKFLOW_GOVERNANCE.md`
- Source commit: Sources 1–2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — fixed-commit lines 149-162; Source 2 — `Document Authority`, `Governing Principles`
- First practiced: 2026-09-02
- Last verified: 2026-09-02
- Technical authority: Groundary project governance evidence; external authority not required for this project boundary
- Sensitivity: INTERNAL
- Notes: Derived from the fixed-commit project security and documentation-authority rules; it does not claim current runtime state.

本目录未来只保存从 Groundary 实践中提炼、脱敏且可跨项目复用的案例总结。运行真相、源码和项目治理仍以 Groundary 仓库为准，本知识库不会自动覆盖项目文档。

禁止复制 Groundary 的 Secret、数据库、用户数据、原始供应商响应、未脱敏日志或实时运行状态。需要引用时使用稳定链接、提交哈希和必要的脱敏摘要。

## 案例索引

### Provenance

- Source scope: 本来源块覆盖“案例索引”表格的 8 个案例链接、说明、状态、来源项目和最后核验日期。
- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `docs/AI_WORKFLOW_GOVERNANCE.md`; Source 3 — `docs/LEARNING_KNOWLEDGE_BASE.md`
- Source commit: Sources 1–3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Brand Rename — Groundary（软改名）— 2026-09-02（CURRENT）`, `已知问题 — 测试统计文件隔离`, `Stage 6 Runtime（approved facts）`, `Post-Stage-6 Research — Question Decomposition V0.3 Controlled Runtime Closeout`, `PRODUCT_BINDING_PROJECTION_BUG（root cause → CLOSED）`, fixed-commit lines 1221-1244, `Identity & Security Model`; Source 2 — `Governing Principles`, `Evidence Authority Chain`; Source 3 — `Evidence-first 与 Real MCP Safety Boundary`, `Agent Engineering 基础知识`
- First practiced: 2026-08-21
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all three listed sources. The deployment entry uses fixed-commit line numbers to avoid restoring the cloud-provider heading. The table is a navigation summary; each linked case separately lists its full implementation and test sources. Groundary project documentation and source code remain factual authorities.

| 案例 | 一句话说明 | 状态 | 来源项目 | 最后核验日期 |
|---|---|---|---|---|
| [Evidence-Governed Runtime](./EVIDENCE_GOVERNED_RUNTIME.md) | 以 R1–R6 分层说明 Evidence、Runtime、验证和确定性计算的权威边界。 | `DERIVED` | Groundary | 2026-09-02 |
| [AI Workflow Governance](./AI_WORKFLOW_GOVERNANCE_CASE.md) | 用角色分离、证据链和独立提交门控治理多 Agent 协作。 | `DERIVED` | Groundary | 2026-09-02 |
| [Identity and Credential](./IDENTITY_AND_CREDENTIAL_CASE.md) | 分离用户身份、资源所有权、加密存储、Resolver 和 Provider Context。 | `DERIVED` | Groundary | 2026-09-02 |
| [Test Isolation](./TEST_ISOLATION_CASE.md) | 如实记录被忽略的统计文件仍受测试污染且尚未修复。 | `DERIVED` | Groundary | 2026-09-02 |
| [Brand Rename](./BRAND_RENAME_CASE.md) | 总结用户品牌软改名与兼容运行标识保留策略。 | `DERIVED` | Groundary | 2026-09-02 |
| [Deployment Reverse Proxy](./DEPLOYMENT_REVERSE_PROXY_CASE.md) | 说明静态页可达不等于 API 可达及分层验证方法。 | `DERIVED` | Groundary | 2026-09-02 |
| [Question Decomposition](./QUESTION_DECOMPOSITION_CASE.md) | 总结版本化语义契约、fail-closed adapter 与评测驱动开发。 | `DERIVED` | Groundary | 2026-09-02 |
| [Projection Bug](./PROJECTION_BUG_CASE.md) | 通过安全结构诊断定位 presentation 与 canonical identity 的投影缺陷。 | `DERIVED` | Groundary | 2026-09-02 |

## 案例来源模板

新增案例时复制以下模板，并遵守 [Knowledge Provenance Standard](../../governance/KNOWLEDGE_PROVENANCE_STANDARD.md)。本模板只建立结构，不迁移 Groundary 的具体知识或运行事实。

```markdown
## <Case title>

- Knowledge type: CASE_STUDY
- Knowledge status: <VERIFIED | DERIVED | HISTORICAL | PLANNED | UNVERIFIED | DEPRECATED>
- Origin project: Groundary
- Source repository: <Groundary repository name and stable URL>
- Source document: <project-authoritative document path>
- Source commit: <40-character full Git commit hash>
- Source section: <heading, anchor, or lines>
- First practiced: <YYYY-MM-DD | UNKNOWN>
- Last verified: <YYYY-MM-DD | NEVER>
- Technical authority: <official specification/documentation or Groundary authority document>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <redaction, applicability, or None>

### Context

<Sanitized context and constraints>

### Decision and evidence

<Decision, alternatives, and traceable evidence>

### Result and reusable lesson

<Observed result, limitations, and reusable lesson>
```
