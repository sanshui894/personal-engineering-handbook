# Personal VPN Runbook

**Status:** `CURRENT`

本 Runbook 适用于在个人 Linux 云服务器上部署和维护 WireGuard VPN。它沉淀了 2026-09-05 真实节点维护（新增客户端 Peer）时验证的操作步骤，并把既有骨架的已知事实合并为可执行手册。所有可填写值必须先用 `replace-with-*` 惰性占位符替换并核验，未替换时命令应安全退出。

### Provenance

- Source scope: 本来源块覆盖本文件从"架构概述"到"常见错误"的全部正文、命令示例与管理规范；"安全边界"作为通用约束适用，不单独产生新知识。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: N/A (not project-derived) — 来自个人服务器运维实践，不属于任何软件项目
- Source repository: N/A (non-Git source) — 一手证据是用户 2026-09-05 在个人 Ubuntu VPS 上执行的 WireGuard 节点维护（新增客户端 Peer）；官方权威单列于 Technical authority
- Source document: N/A (non-Git source)
- Source commit: N/A (non-Git source)
- Source section: 维护步骤：新增客户端 Peer 全流程（客户端生成密钥 → 服务端保存客户端 PublicKey → 客户端保存服务端 PublicKey → 分配 VPN IP → reload/restart `wg-quick@wg0` → `latest handshake` 核验）
- First practiced: 2026-09-05
- Last verified: 2026-09-05
- Technical authority: WireGuard 官方文档 — https://www.wireguard.com/; WireGuard Quick Start — https://www.wireguard.com/quickstart/; wg(8) 与 wg-quick(8) man pages; systemd.service — https://www.freedesktop.org/software/systemd/man/latest/systemd.service.html
- Sensitivity: INTERNAL
- Notes: 新增 Peer 流程与 Peer/PublicKey/PrivateKey 双向关系在 2026-09-05 现场验证；服务安装与删除 Peer 流程按官方文档整理，其中删除流程尚未完整演练。正文不含真实公网/内网地址、VPN 网段、主机名、用户名、公钥、私钥或完整客户端配置；示例必须替换占位符后才可用，且不允许直接拼成可用凭据。

## 架构概述

客户端设备通过独立 Peer 身份连接服务端 UDP 端口，经 VPN 虚拟网段进入 `wg0`，再由服务器通过出口网卡执行转发和 NAT。一个设备一个 Peer，不共享密钥或完整客户端配置。

服务名 `wg-quick@wg0` 对应配置文件 `/etc/wireguard/wg0.conf`，文件名即接口名（`wg-quick@<接口>.service`）。`wg0.conf` 是接口配置的唯一事实来源；修改后必须 reload/restart 才生效。

## 概念速览与交叉引用

| 想了解 | 位置 |
|---|---|
| VPS 与 VPN 区别、WireGuard 工作方式、PublicKey/PrivateKey 双向关系、Peer 即身份、为何不共享私钥、systemd 服务与应用进程区别 | [Learning Knowledge Base § WireGuard 与个人 VPN 认识](../../learning/LEARNING_KNOWLEDGE_BASE.md) |
| 新服务器首次接管审计、Windows → Ubuntu SSH、GitHub SSH access | [Ubuntu Server Onboarding Runbook](../server/UBUNTU_SERVER_ONBOARDING_RUNBOOK.md) |
| 当前实际网段/地址/设备分配（私有档案） | [WireGuard Networks](../../infrastructure/WIREGUARD_NETWORKS.md)、[IP Inventory](../../infrastructure/IP_INVENTORY.md) |
| 事实核验历史（追加式） | [Verification Log](../../infrastructure/VERIFICATION_LOG.md) |

## 前置条件

- 已完成新服务器首次接管审计（见 Ubuntu Server Onboarding Runbook），并有独立恢复通道。
- 已规划不冲突的 VPN 网段、服务端地址与每设备唯一地址（见下文"Peer 与地址管理规范"）。
- 能管理云安全组、主机防火墙和 systemd 服务。
- 已阅读 `governance/SECURITY_AND_SECRETS.md`。

## Ubuntu VPS 初始化与 WireGuard 安装

1. 在云安全组只对 UDP 放行 WireGuard 监听端口（默认 `replace-with-wireguard-listen-port`，如 51820），尽量收敛来源范围；移动设备 NAT 出口变化频繁时按现状放宽并接受风险记录。不要对 TCP 放行该端口。
2. SSH 登录后用系统包管理器安装：

   ```bash
   sudo apt update
   sudo apt install wireguard
   wg --version
   ```

   - 若提示内核模块未加载，执行 `sudo modprobe wireguard`；Ubuntu 标准内核通常已内置或由 `wireguard-tools` 一并提供。
3. 开启并持久化 IP forwarding：

   ```bash
   echo 'net.ipv4.ip_forward = 1' | sudo tee /etc/sysctl.d/99-wireguard.conf
   sudo sysctl --system
   sysctl net.ipv4.ip_forward
   ```

   输出 `net.ipv4.ip_forward = 1` 后再继续。
4. 若启用主机防火墙（如 ufw），需放行 UDP 监听端口并允许 `wg0` 转发；规则效果必须在服务配置完成后用真实客户端验证，不能只检查规则文件。

## 服务端配置（wg0.conf）

1. 在服务器本机生成服务端密钥对：

   ```bash
   cd /etc/wireguard
   sudo wg genkey | sudo tee privatekey-server | wg pubkey | sudo tee publickey-server
   sudo chmod 600 privatekey-server
   ```

   - 私钥文件绝不离开服务器，绝不进入文档、聊天或 Git。公钥可对外提供。
2. 编写 `/etc/wireguard/wg0.conf`（模板，占位符必须替换）：

   ```ini
   [Interface]
   Address = replace-with-server-vpn-address/24
   ListenPort = replace-with-wireguard-listen-port
   PrivateKey = replace-with-server-private-key
   # 出口转发与 NAT；真实出口网卡名先用 `ip route` 核验再替换
   PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o replace-with-egress-interface -j MASQUERADE
   PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o replace-with-egress-interface -j MASQUERADE
   # 每个客户端追加一个 [Peer] 块，见"新增 Peer 流程"
   ```

3. 启动并设置开机自启：

   ```bash
   sudo systemctl enable wg-quick@wg0
   sudo systemctl start wg-quick@wg0
   systemctl status wg-quick@wg0
   sudo wg show wg0
   ```

## 新增 Peer 流程（2026-09-05 现场验证）

新增一台设备时按以下顺序执行。核心是**密钥只在各自本机生成，双方只互换公钥**。

1. 在**客户端**生成独立密钥对：

   ```bash
   wg genkey | tee /path/privatekey-client | wg pubkey
   ```

   - 管道第一段输出是客户端私钥，第二段是客户端公钥。私钥只保存在客户端本机，绝不发送给任何人（包括服务端）。
2. **服务端保存客户端 PublicKey**：在 `/etc/wireguard/wg0.conf` 追加 `[Peer]` 块，只填公钥与唯一地址：

   ```ini
   [Peer]
   PublicKey = replace-with-client-public-key
   AllowedIPs = replace-with-client-vpn-address/32
   ```

3. **客户端保存服务端 PublicKey**：从服务器取服务端公钥（`sudo wg show wg0 public-key` 或 `/etc/wireguard/publickey-server`），经可信带外渠道送达客户端，填入客户端配置：

   ```ini
   [Interface]
   PrivateKey = replace-with-client-private-key
   Address = replace-with-client-vpn-address/32

   [Peer]
   PublicKey = replace-with-server-public-key
   Endpoint = replace-with-server-public-address:replace-with-wireguard-listen-port
   AllowedIPs = 0.0.0.0/0   # 全隧道；仅需访问服务器/内网时收敛为对应子网
   PersistentKeepalive = 25 # 客户端位于 NAT 后时建议
   ```

   - 公钥方向最容易放反：服务端 `[Peer]` 存**客户端**公钥，客户端 `[Peer]` 存**服务端**公钥；放反会导致握手无法建立。
4. **分配 VPN IP**：从地址分配表取一个未被占用的唯一地址，服务端 `AllowedIPs` 与客户端 `Address` 必须一致（见"Peer 与地址管理规范"）。
5. **reload/restart 服务端**：

   ```bash
   sudo wg syncconf wg0 <(wg-quick strip wg0)  # 无损应用配置，不打断既有 Peer 的握手
   # 或完整重载：
   # sudo systemctl restart wg-quick@wg0
   ```

6. **验证 latest handshake**：

   ```bash
   sudo wg show wg0
   ```

   确认新 Peer 的 `latest handshake` 出现并随时间更新、`transfer` 计数增长；再从客户端 `ping` 隧道地址并验证出口连通。最后把观测追加到 `infrastructure/VERIFICATION_LOG.md`。

## 删除 Peer 流程

> 状态说明：按 WireGuard 官方文档整理，尚未完整演练。执行前后都以 `sudo wg show wg0` 记录 Peer 列表。

1. 先记录待删除 Peer 的公钥或命名（用于审计），再在运行时移除：

   ```bash
   sudo wg set wg0 peer replace-with-peer-public-key remove
   ```

2. 编辑 `/etc/wireguard/wg0.conf`，删除对应 `[Peer]` 块（保证重启后不复活），然后 `sudo wg syncconf wg0 <(wg-quick strip wg0)` 或 `sudo systemctl restart wg-quick@wg0`。
3. 核验 `sudo wg show wg0` 不再列出该 Peer，`latest handshake` 不再更新。
4. 撤销客户端侧材料：通知设备删除客户端配置；清除所有保存过该配置副本或二维码的位置。完整客户端配置与二维码含私钥，不得截图、归档或进入仓库。
5. 释放的 VPN IP 按地址管理规范决定是否复用；把撤销记录追加到核验日志。

## 服务管理

| 操作 | 命令 |
|---|---|
| 查看状态 | `systemctl status wg-quick@wg0` |
| 启动 / 停止 | `sudo systemctl start|stop wg-quick@wg0` |
| 重载配置 | `sudo systemctl restart wg-quick@wg0` |
| 开机自启 | `sudo systemctl enable|disable wg-quick@wg0` |
| 无损应用配置 | `sudo wg syncconf wg0 <(wg-quick strip wg0)` |
| 接口与 Peer 详情 | `sudo wg show wg0`；流量 `sudo wg show wg0 transfer` |
| 服务日志 | `journalctl -u wg-quick@wg0 -n 50` |
| 运行时移除单个 Peer | `sudo wg set wg0 peer <public-key> remove` |

- 修改 `wg0.conf` 后必须 reload/restart 才生效；`wg show` 显示的是内核接口当前状态，两者可能短暂不一致。
- `systemd active` 不等于链路可用：active 只说明 unit 已拉起接口，`latest handshake` 与流量才是链路证据。

## Peer 与地址管理规范

**命名规则**

- 每个 Peer 在服务端配置、分配记录、核验日志三处使用同一可读名称，便于故障定位。建议 `<设备类型>-<系统>-<所有者>-<序号>`，小写连字符，例如 `replace-with-device-owner`。命名只用于人读，安全边界始终是公钥本身。

**VPN IP 分配规则**

- 选定一个私网 `/24` 网段；服务端固定取最低可用地址（如 `.1`），Peer 从 `.2` 起顺序分配，每个 Peer 一个唯一 `/32` 地址，高位地址段留给特殊用途（如路由 Peer）。
- 服务端 `AllowedIPs = <peer>/32` 与客户端 `Address` 必须一致；同一地址禁止分配给两个 Peer。
- 当前实际分配记录只维护在私有档案 `infrastructure/WIREGUARD_NETWORKS.md` 与 `infrastructure/IP_INVENTORY.md`，公开文档不出现真实网段值。

**生命周期管理**

- 创建：分配唯一地址与命名 → 客户端生成密钥 → 公钥互换 → 配置经可信渠道下发 → 握手与连通核验 → 追加核验日志。
- 使用中：定期用 `wg show` 核验握手/流量，确认密钥未被泄露。
- 密钥轮换：先以新密钥创建新 Peer 并验证连接，再撤销旧 Peer，最后清理旧配置副本。
- 退役：按"删除 Peer 流程"移除服务端授权，撤销设备端配置，密钥材料随设备退役删除。
- 私钥与完整配置只存在于设备本机或密码管理器；仓库 `.gitignore` 已忽略 `*.conf`、`*.key`，但忽略规则不等于安全保证，仍不得把配置写入仓库。

## 核验

- `sudo wg show wg0`：核对接口、监听端口、Peer 数以及每个 Peer 的 `latest handshake` 与 `transfer`。**不**把任何密钥正文复制进文档。
- 分层核验：握手 → 双向流量 → DNS → 互联网出口 → 实际测速；分别通过后再认为链路可用。
- 全隧道配置下确认客户端出口 IP 已是服务端出口；测速结果记录日期、客户端网络与测试条件，不作为长期承诺。

## 排错方向

- `latest handshake` 长期为空：核对密钥方向与配对、`AllowedIPs` 是否重叠、客户端 `Endpoint` 是否为服务端公网地址:端口、云安全组与主机防火墙是否放行 UDP、服务是否在监听（`sudo ss -lunp` 确认 UDP 端口）。
- 能握手但无法访问外网：检查服务端 IP forwarding、NAT `MASQUERADE`、出口网卡名是否与 `PostUp` 一致。
- 能握手但大流量/视频卡顿或特定站点失败：先怀疑 MTU，客户端调低 MTU（1280–1420 区间）分段测试。
- 客户端在移动网络或家庭 NAT 后掉线：给客户端配置 `PersistentKeepalive`。
- DNS 与外网行为不一致（能解析但内容打不开、内网域名失败）：区分隧道 DNS 与出口链路，检查本地解析器（如 systemd-resolved）。
- 配置修改后不生效：先 reload/restart，再对比 `wg show` 与 `wg0.conf`，以 `wg0.conf` 为准重新应用。
- 只有个别 Peer 不通而其他正常：多为地址/`AllowedIPs` 冲突或公钥错配，用 `wg show wg0` 对比。

## 常见错误

- 服务端 `[Peer]` 误填成服务端自己的公钥，或客户端/服务端公钥放反。
- 两端 `AllowedIPs` 与 `Address` 不一致，或同一地址分配给多个 Peer。
- 把含私钥的客户端配置或二维码发送到明文渠道、截图或提交仓库。
- `phone.conf` 不存在：先为设备生成独立 Peer 和配置，不要假设文件已存在。
- `qrencode` 不是配置生成器：它只编码已有输入，不能创建 WireGuard 身份或补全配置。
- 重复使用同一客户端配置：会造成身份、地址与握手互相覆盖。
- 云安全组未放行 UDP：服务本机运行不代表公网可达。
- 认为 `systemctl status` active 就代表链路可用：还要看握手与流量。

## 安全边界

禁止在本 Runbook 中加入真实私钥、公钥正文、二维码、密码或完整可用客户端配置。示例只能使用不可用占位符，且不允许组合成可直接部署的凭据。执行命令前先确认当前主机与目标用户；密钥生成与配置下发一律走可信渠道。
