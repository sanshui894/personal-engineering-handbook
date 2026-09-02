# Agent Working Rules

## 必读顺序

修改本仓库前必须完整阅读：

1. `README.md`
2. `AGENTS.md`
3. `governance/DOCUMENT_GOVERNANCE.md`
4. `governance/SECURITY_AND_SECRETS.md`

## 事实状态

所有事实必须明确区分：

- `CURRENT`：在标注日期已核验且当前采用。
- `HISTORICAL`：曾经成立，仅作为历史记录。
- `PLANNED`：已提出或批准但尚未完成。
- `UNVERIFIED`：尚无充分证据或需要重新核验。

每条基础设施事实必须包含来源、最后核验日期、状态以及是否敏感。

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
- Knowledge Steward：术语、索引、状态与重复内容维护。
- 用户：架构、隐私和发布范围的最终决定者。

Agent 对其他仓库默认只有只读权限；任何跨仓库迁移都需要明确范围，并先执行脱敏和通用性判断。
