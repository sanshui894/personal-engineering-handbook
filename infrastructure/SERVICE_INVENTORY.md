# Service Inventory

来源：用户提供的初始化事实。最后核验日期：2026-09-02。敏感级别：`PRIVATE-INFRASTRUCTURE`。

| 服务 | 协议/端口 | 绑定地址 | 用途 | 外部暴露状态 | 状态 | 最后核验日期 |
|---|---|---|---|---|---|---|
| SSH | TCP 22 | `UNVERIFIED` | 服务器远程管理 | 云安全组暴露状态 `UNVERIFIED` | `CURRENT` | 2026-09-02 |
| Groundary | TCP 3001 | `UNVERIFIED` | Groundary 应用服务 | 云安全组暴露状态 `UNVERIFIED` | `CURRENT` | 2026-09-02 |
| WireGuard | UDP 51820 | `UNVERIFIED` | 个人 VPN 接入 | 云安全组暴露状态 `UNVERIFIED` | `CURRENT` | 2026-09-02 |
| systemd-resolved | 本地 TCP/UDP 53 | 本地解析器；精确地址 `UNVERIFIED` | 主机 DNS 解析 | 预期仅本地，现场状态 `UNVERIFIED` | `CURRENT` | 2026-09-02 |

端口存在不等于公网可达。监听地址、主机防火墙与腾讯云安全组需要分别核验，不得由其中一项推断其他项。
