# Learning Knowledge Base

**Status:** `MIGRATION_PENDING`

本文件定位为跨项目学习知识库，用于沉淀能够复用的理解过程和工程知识。

内容必须区分：

- 通用知识：不依赖单一项目且经过核验的概念或方法；
- 实验记录：保留条件、过程、结果与局限，不提升为通用结论；
- 项目案例：说明来源项目和适用边界，链接到对应案例目录。

初始化阶段不直接复制尚未分类、未脱敏或仍承担 Groundary 当前事实职责的内容。

所有新增学习知识和待学习问题必须遵守 [Knowledge Provenance Standard](../governance/KNOWLEDGE_PROVENANCE_STANDARD.md)。`PLANNED` 或 `UNVERIFIED` 条目只能描述问题、假设与验证计划，不得写成已验证事实。

## 学习知识来源模板

```markdown
## <Learning topic>

- Knowledge type: <GENERAL | PROJECT_SPECIFIC>
- Knowledge status: <VERIFIED | DERIVED | HISTORICAL | PLANNED | UNVERIFIED | DEPRECATED>
- Origin project: <project or N/A (not project-derived)>
- Source repository: <repository name and stable URL>
- Source document: <path or stable URL>
- Source commit: <40-character full Git commit hash or N/A (non-Git source)>
- Source section: <heading, anchor, or lines>
- First practiced: <YYYY-MM-DD | UNKNOWN>
- Last verified: <YYYY-MM-DD | NEVER>
- Technical authority: <official specification/documentation or UNVERIFIED>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <assumptions, applicability, and limitations>

### Question

<Question being investigated>

### Evidence and reasoning

<Evidence, experiment conditions, and reasoning. DERIVED entries list every contributing source.>

### Conclusion and limits

<Conclusion, confidence, and boundaries>
```

## 待学习条目来源模板

```markdown
## <Question to learn>

- Knowledge type: LEARNING_BACKLOG
- Knowledge status: <PLANNED | UNVERIFIED>
- Origin project: <project or N/A (not project-derived)>
- Source repository: <repository name and stable URL, or source carrier>
- Source document: <path or stable URL>
- Source commit: <40-character full Git commit hash or N/A (non-Git source)>
- Source section: <section that raised the question>
- First practiced: UNKNOWN
- Last verified: NEVER
- Technical authority: <candidate official source or UNVERIFIED>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <why this matters and required evidence>

- Question: <question, not a factual claim>
- Verification plan: <steps and acceptance criteria>
```
