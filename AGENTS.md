# Agent Working Rules

## 必读顺序

修改本仓库前必须完整阅读：

1. `README.md`
2. `AGENTS.md`
3. `governance/DOCUMENT_GOVERNANCE.md`
4. `governance/KNOWLEDGE_PROVENANCE_STANDARD.md`
5. `governance/SECURITY_AND_SECRETS.md`

## 事实状态

所有事实必须明确区分：

- `CURRENT`：在标注日期已核验且当前采用。
- `HISTORICAL`：曾经成立，仅作为历史记录。
- `PLANNED`：已提出或批准但尚未完成。
- `UNVERIFIED`：尚无充分证据或需要重新核验。

每条基础设施事实必须包含来源、最后核验日期、状态以及是否敏感。

所有知识必须遵守 [`governance/KNOWLEDGE_PROVENANCE_STANDARD.md`](./governance/KNOWLEDGE_PROVENANCE_STANDARD.md)。无来源、来源字段缺失、来源无法定位或 Git 来源未使用完整 commit hash 的条目属于阻断问题，不得合入 `main`。

## 提交前来源检查

Agent 在提交前必须逐项检查本轮新增或修改的知识单元：

- 来源字段完整且与知识类型、状态一致；
- `Origin project` 与 `Technical authority` 分开记录；
- 共享来源块声明了精确的 `Source scope`；
- `DERIVED` 列出全部参与推导的来源；
- `PLANNED` 和 `UNVERIFIED` 未被表述成已验证事实；
- 来源引用不包含 Secret、数据库、原始响应或其他禁止内容。

## 禁止事项

Agent 不得：

- 写入私钥、密码、Token、Cookie、JWT、完整客户端配置或其他 Secret；
- 猜测动态运行状态；
- 自动修改其他项目仓库；
- 把项目特例伪装成通用最佳实践；
- 重写历史核验记录。

## 冲突处理

当前事实与历史记录冲突时，更新当前清单并保留历史核验日志，不得用新事实覆盖旧记录。无法确认的冲突标记为 `UNVERIFIED` 并注明待核验项。

## 维护责任

- Codex：结构、实现、迁移和技术核验。
- Knowledge Steward：术语、索引、状态与重复内容维护，并负责发现来源漂移、失效链接和来源继承范围失真。
- 用户：架构、隐私和发布范围的最终决定者。

Agent 对其他仓库默认只有只读权限；任何跨仓库迁移都需要明确范围，并先执行脱敏和通用性判断。
