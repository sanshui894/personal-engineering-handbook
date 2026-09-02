# Development Cheatsheet

**Status:** `MIGRATION_PENDING`

本文件定位为跨项目命令速查。初始化阶段暂不复制 Groundary 内容。

后续只接收可跨项目复用的 Linux、Git、Node.js、SSH、PM2、Nginx 和测试命令。每条命令应说明用途、前置条件、预期结果、安全风险和最后核验日期。

项目专属路径、进程名、部署参数和一次性执行命令必须放入对应案例或项目 Runbook，不得混入通用速查。

## 章节来源模板

新增命令章节时复制以下模板，并遵守 [Knowledge Provenance Standard](../governance/KNOWLEDGE_PROVENANCE_STANDARD.md)。共享来源块只覆盖 `Source scope` 明确列出的内容。

```markdown
## <Command topic>

<!-- Source scope: <exact command entries or subsections covered> -->

- Knowledge type: <GENERAL | PROJECT_SPECIFIC>
- Knowledge status: <VERIFIED | DERIVED | HISTORICAL | PLANNED | UNVERIFIED | DEPRECATED>
- Origin project: <project or N/A (not project-derived)>
- Source repository: <repository name and stable URL>
- Source document: <path or stable URL>
- Source commit: <40-character full Git commit hash or N/A (non-Git source)>
- Source section: <heading, anchor, or lines>
- First practiced: <YYYY-MM-DD | UNKNOWN>
- Last verified: <YYYY-MM-DD | NEVER>
- Technical authority: <official command documentation or UNVERIFIED>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <supported versions, applicability, or None>

### `<command with inert placeholders>`

- Purpose: <purpose>
- Preconditions: <preconditions>
- Expected result: <expected result>
- Safety risks: <risk and rollback>
```
