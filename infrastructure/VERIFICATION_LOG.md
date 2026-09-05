# Verification Log

本文件是追加式核验记录。禁止删除、重排或改写旧记录；新核验必须追加新日期和证据来源。清单中的 CURRENT 状态变化不覆盖这里的历史观测。

## 2026-09-02 — Initial user-reported baseline

- 记录来源：用户在仓库初始化任务中提供。
- 核验方式：本轮未对以下运行状态执行现场命令，因此全部标记为 `USER_REPORTED`。
- 敏感级别：`PRIVATE-INFRASTRUCTURE`。

| 观测 | 状态 | 说明 |
|---|---|---|
| WireGuard 服务运行 | `USER_REPORTED` | 未在本轮现场执行服务核验 |
| Windows Peer 已握手并产生流量 | `USER_REPORTED` | 未记录密钥、配置或握手身份材料 |
| Phone Peer 已配置 | `USER_REPORTED` | 未复制完整客户端配置或二维码 |
| VPN 出口能够访问互联网 | `USER_REPORTED` | 未在本轮复测出口 |
| 测速约 26 Mbps | `USER_REPORTED` / `HISTORICAL_OBSERVATION` | 仅代表当时观测值，不是长期性能承诺 |
| Groundary v4.2.0 运行健康 | `USER_REPORTED` | 本知识库不承担 Groundary 运行真相 |
| GitHub/Gitee Groundary `master` 已同步 | `USER_REPORTED` | 本轮未从本仓库再次查询两个远端 |

## 2026-09-05 — WireGuard 节点维护（新增客户端 Peer）

- 记录来源：用户在真实 WireGuard 节点维护中的现场操作结果（服务端为个人 Ubuntu VPS）。
- 核验方式：本轮仓库会话未执行现场命令，以下为用户操作并报告的 `USER_REPORTED` 事实；已据其整理 runbook 与学习知识，不记录任何密钥正文、客户端配置或真实地址。
- 敏感级别：`PRIVATE-INFRASTRUCTURE`。

| 观测 | 状态 | 说明 |
|---|---|---|
| 新增客户端 Peer 全流程执行成功 | `USER_REPORTED` | 客户端生成密钥 → 服务端保存客户端 PublicKey → 客户端保存服务端 PublicKey → 分配唯一 VPN IP → reload/restart `wg-quick@wg0` → `latest handshake` 核验通过 |
| Peer / PublicKey / PrivateKey 双向关系已学习并验证 | `USER_REPORTED` | 与 WireGuard 官方文档一致；概念已入 Learning Knowledge Base |
| 新 Peer 握手正常并持续更新 | `USER_REPORTED` | 未记录握手身份材料与地址 |
| VPN 服务端仍可正常 reload/restart | `USER_REPORTED` | 流程中已执行并确认无配置错误 |
