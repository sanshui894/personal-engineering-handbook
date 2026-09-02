# Groundary Case Study

Groundary 原名 MH-ASIN，当前项目仓库为 <https://github.com/sanshui894/Groundary>。

项目定位：**Evidence-Governed Decision System**。

本目录未来只保存从 Groundary 实践中提炼、脱敏且可跨项目复用的案例总结。运行真相、源码和项目治理仍以 Groundary 仓库为准，本知识库不会自动覆盖项目文档。

禁止复制 Groundary 的 Secret、数据库、用户数据、原始供应商响应、未脱敏日志或实时运行状态。需要引用时使用稳定链接、提交哈希和必要的脱敏摘要。

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
