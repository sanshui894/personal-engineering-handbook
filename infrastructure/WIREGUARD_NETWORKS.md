# WireGuard Networks

- 状态：`CURRENT`
- 来源：用户提供的初始化事实
- 最后核验日期：2026-09-02
- 敏感级别：`PRIVATE-INFRASTRUCTURE`
- 是否敏感：是

| 字段 | 当前值 |
|---|---|
| 接口 | `wg0` |
| VPN 网段 | `10.66.66.0/24` |
| 服务端 | `10.66.66.1/24` |
| Windows Peer | `10.66.66.2/32` |
| Phone Peer | `10.66.66.3/32` |
| 监听端口 | UDP 51820 |
| 出口网卡 | `eth0` |
| NAT | `MASQUERADE` |
| IP forwarding | `enabled` |
| Endpoint | `43.153.168.237:51820` |

本档案不记录任何公钥或私钥正文。一个 Peer 配置代表一个设备身份，不能由多人或多台设备随意共享；每台设备应使用独立密钥和独立隧道地址。
