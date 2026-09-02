# Personal Engineering Handbook

个人可复用的工程知识库，用于沉淀跨项目通用的软件开发知识、Linux 与服务器运维、Git 与部署流程、网络与 WireGuard、AI/Agent 协作治理、技术术语、学习记录和项目案例。

Groundary 是项目仓库；本仓库是跨项目知识库。项目运行真相仍以各项目仓库为准，本知识库不会自动覆盖项目文档。仓库当前默认私有，禁止存放任何 Secret。

## 目录导航

| 分类 | 文件 | 用途 |
|---|---|---|
| Agent 规则 | [AGENTS.md](./AGENTS.md) | Agent 进入仓库后的工作边界 |
| 文档治理 | [DOCUMENT_GOVERNANCE.md](./governance/DOCUMENT_GOVERNANCE.md) | 文档分类、状态与历史维护规则 |
| 安全 | [SECURITY_AND_SECRETS.md](./governance/SECURITY_AND_SECRETS.md) | 敏感信息与 Secret 边界 |
| 命令速查 | [DEVELOPMENT_CHEATSHEET.md](./reference/DEVELOPMENT_CHEATSHEET.md) | 跨项目工程命令 |
| 技术术语 | [TECH_GLOSSARY.md](./reference/TECH_GLOSSARY.md) | 跨项目术语表 |
| 学习知识 | [LEARNING_KNOWLEDGE_BASE.md](./learning/LEARNING_KNOWLEDGE_BASE.md) | 通用知识、实验与案例学习 |
| 服务器档案 | [SERVER_INVENTORY.md](./infrastructure/SERVER_INVENTORY.md) | 主机事实清单 |
| IP 档案 | [IP_INVENTORY.md](./infrastructure/IP_INVENTORY.md) | 地址、用途与敏感级别 |
| 服务档案 | [SERVICE_INVENTORY.md](./infrastructure/SERVICE_INVENTORY.md) | 服务端口与暴露状态 |
| WireGuard 网络 | [WIREGUARD_NETWORKS.md](./infrastructure/WIREGUARD_NETWORKS.md) | VPN 网络摘要 |
| 核验日志 | [VERIFICATION_LOG.md](./infrastructure/VERIFICATION_LOG.md) | 追加式事实核验历史 |
| WireGuard Runbook | [PERSONAL_VPN_RUNBOOK.md](./runbooks/wireguard/PERSONAL_VPN_RUNBOOK.md) | 个人 VPN 安全操作骨架 |
| Groundary 案例 | [case-studies/groundary/README.md](./case-studies/groundary/README.md) | 可复用项目案例入口 |

## 基本原则

- 通用知识与项目事实分开保存。
- 动态事实必须标明状态、来源和最后核验日期。
- 案例总结不替代源项目的源码、运行文档或治理记录。
- 提交前执行敏感信息扫描；即使仓库私有，也不得提交 Secret。
