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
