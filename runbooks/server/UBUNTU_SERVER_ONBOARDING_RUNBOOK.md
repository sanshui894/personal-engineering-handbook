# Ubuntu Server Onboarding Runbook

**Status:** `CURRENT`

本 Runbook 沉淀从个人运维实践中验证的通用操作：从 Windows 首次 SSH 连接 Ubuntu 服务器、终端剪贴板与信号行为、fresh Ubuntu server 审计、从 application server 配置 GitHub SSH access，以及 Node application host 基线核验。每条事实均已脱敏；所有可填写值必须先用 `replace-with-*` 惰性占位符替换并核验，未替换时命令应安全退出。

### Provenance

- Source scope: 本来源块覆盖本文件全部章节（“Windows → Ubuntu SSH 首次连接”“终端剪贴板与信号行为”“Fresh Ubuntu server 审计清单”“GitHub SSH access from application server”“Node application host baseline”“安全边界”）。
- Knowledge type: GENERAL
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `docs/PROJECT_STRUCTURE.md`; Source 3 — `docs/WEEKLY_PROJECT_REPORT.md`
- Source commit: Sources 1–3 — `6a842bdf070e585676f17f23a5cca49147d5176c`
- Source section: Source 1 — §2.4 `Repositories and environment roles`（新 application server 核验事实）; Source 2 — `服务器角色与环境边界`（角色边界）; Source 3 — `State Milestone（2026-09-03 追加）`
- First practiced: 2026-09-03
- Last verified: 2026-09-03
- Technical authority: OpenSSH manual — https://www.openssh.com/manual.html; GitHub SSH documentation — https://docs.github.com/en/authentication; Ubuntu Server documentation — https://ubuntu.com/server/docs; Node.js documentation — https://nodejs.org/; PM2 documentation — https://pm2.keymetrics.io/; Nginx documentation — https://nginx.org/
- Sensitivity: INTERNAL
- Notes: 操作发生在受控的个人服务器上；正文不含真实公网 IP、主机名、用户账户名、SSH 私钥、Git 仓库作者或真实凭据，示例一律使用无效占位符。PM2 boot startup 未宣称已完成；本文件不记录任何 Groundary 运行状态。

## Windows → Ubuntu SSH 首次连接

适用：从 Windows 上的 PowerShell / Windows Terminal 首次登录一台全新 Ubuntu 服务器。

1. 先做只读确认：目标地址、用户名、服务器是否可达、是否需要先到云控制台确认开机与网络。
2. 正常登录流程：

   ```bash
   ssh replace-with-user@replace-with-host
   ```

   - 首次连接时 OpenSSH 会显示 host key fingerprint 并询问是否接受。
   - 应通过受信任的带外渠道（云控制台、已核验的管理记录）核对 fingerprint；核对一致后输入 `yes` 接受并继续。
   - 若服务器允许 password authentication 且本机尚未配置 key，会出现密码提示。
3. `ssh -vvv` 只用于诊断：

   ```bash
   ssh -vvv replace-with-user@replace-with-host
   ```

   - 详细输出用来定位 handshake、host key、认证方法与断开原因；它是诊断工具，不是正常登录方式。
4. 诊断后退出 verbose 会话并用普通命令重连：在会话内输入 `exit` 返回本地终端，再执行普通 `ssh`，不要留在 verbose 模式里继续操作。
5. 如果认证失败：先区分 host key 问题、用户名/密码问题与服务器 `sshd` 配置问题（如 `PasswordAuthentication`、`PubkeyAuthentication`）；不要反复盲试密码，也不要直接删除本机 `known_hosts` 中保存的主机指纹而不核对。

经验教训：正常登录只需一条普通 `ssh`；verbose 与 key 生成都是排查工具。host key 变更提示（而非首次提示）属于需要停手核对的信号。

## 终端剪贴板与信号行为

在 SSH 会话或一般终端里，剪贴板与信号不要按习惯假设：

- `Ctrl+C` 在 SSH 终端会话里通常是发送 SIGINT（中断当前程序），不是复制。
- 不要假设 `Ctrl+C` 在终端会话内等于复制；复制/粘贴快捷键因终端而异。
- `Ctrl+Shift+C` / `Ctrl+Shift+V` 可能被当前终端（如 Windows Terminal）用于复制/粘贴，但不是所有终端都支持。
- 鼠标右键的复制/粘贴行为依赖具体终端：有的右键粘贴、有的需要选择文本后右键复制，有的需要中键粘贴。
- 应先用不产生破坏的命令验证客户端行为（例如 `echo test` 后测试选择/复制/粘贴），不要边跑真实操作边猜快捷键。

经验教训：把终端当成一个会回显、有历史、快捷键约定不同的环境来验证，而不是照搬图形界面习惯。

## Fresh Ubuntu server 审计清单

新服务器交接或首次接管时，用只读命令逐项核验并记录日期与证据；不要只相信交接说明。

| 检查项 | 只读命令 | 正常结果 / 注意 |
|---|---|---|
| 主机名 | `hostname` | 输出预期主机名；先确认操作的是目标主机 |
| OS 版本 | `cat /etc/os-release` | 记录 VERSION / PRETTY_NAME |
| CPU | `nproc`（或 `lscpu`） | 核数符合预期；`lscpu` 额外显示型号/架构 |
| RAM | `free -h` | 总量与可用量符合预期 |
| swap | `swapon --show`（配合 `free -h`） | 有则显示设备与大小；无则明确记录为无 |
| disk | `df -h` | 根分区已用/可用空间符合预期 |
| 网络接口 / IP | `ip addr` | 记录接口、内网 IP；公网地址通常不在主机内直接确认 |
| Git | `git --version` | 输出版本号 |
| Node.js | `node --version` | 输出版本号 |
| npm | `npm --version` | 输出版本号 |
| Nginx | `nginx -v`; `systemctl is-active nginx`; `systemctl is-enabled nginx` | 版本、是否 active、是否开机启用 |
| PM2 | `pm2 --version`; `pm2 list` | 版本；`pm2 list` 显示 daemon 与进程（daemon 存在 ≠ 应用开机自启） |

执行约定：全部为只读检查；把结果记录到核验日志并标注日期；不输出 `.env`、密钥、密码或日志正文。

## GitHub SSH access from application server

在 application server 上为 Git 提供 SSH 访问，避免把私钥留在别处或使用临时口令：

1. 在服务器上为部署/访问用途生成 ed25519 key：

   ```bash
   ssh-keygen -t ed25519 -C "replace-with-purpose"
   ```

   - 私钥只保存在服务器本机受控位置（默认 `~/.ssh/`），权限受限；私钥绝不能离开服务器，也绝不能复制进文档、聊天或 Git。
2. 把 `.pub` 公钥内容登记到 Git 提供方（账户级 SSH key 或仓库级 deploy key，按提供方模型选择）。
3. 认证核验（不产生仓库变更）：

   ```bash
   ssh -T git@github.com
   ```

   - 成功时返回欢迎/认证成功消息；错误则先处理 host key 与 key 登记问题。
4. clone 前的非破坏连通性检查：

   ```bash
   git ls-remote git@github.com:replace-with-owner/replace-with-repository.git
   ```

   - 能列出远端引用即证明 SSH 认证与仓库可读性正常。
5. 认证核验通过后再 clone：

   ```bash
   git clone git@github.com:replace-with-owner/replace-with-repository.git
   ```

经验教训：顺序是“生成 key → 登记公钥 → `ssh -T` 验证 → `git ls-remote` 只读检查 → clone”；不在认证未验证时直接 clone，也不在任何环节输出私钥内容。

## Node application host baseline

在 Ubuntu 上核验 Node 应用运行主机的已验证基线：

- Node.js 与 npm：用与安装来源一致的方式核验（如 `node --version` / `npm --version`），不假设包管理器来源。
- PM2：`pm2 --version`、`pm2 list` 确认 daemon 与已托管进程；PM2 daemon 初始化成功与进程 `online` 不等于开机自启。
- Nginx：配置语法 `sudo nginx -t`、服务状态 `systemctl is-active nginx`、开机启用 `systemctl is-enabled nginx`；每次配置变更后先 `nginx -t` 再 reload。
- 依赖安装：`npm install` 成功后再看 `npm audit` 摘要（如 “0 vulnerabilities”），两者是不同事实，不能互相替代。
- 基线不等于运行态：安装了 Node/npm/PM2/Nginx、clone 了仓库、`npm install` 通过，都还不等于应用已启动、`.env` 已配置或已达到 production-ready。

未核验事项必须明确标注：例如“PM2 boot startup / systemd 配置仍需要显式核验”，不得因为 PM2 daemon 存在就宣称开机自启已完成。

## 安全边界

禁止在本 Runbook 中加入真实公网 IP、主机名、用户账户、SSH 私钥、公钥正文、Git 仓库作者、API Key、密码、数据库或日志。示例必须使用无效占位符且不能组合成可用凭据。执行命令前先确认当前主机与目标用户。
