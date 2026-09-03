# Development Cheatsheet

## 文档用途

跨项目可复用的开发与运维命令速查表。命令优先，解释其次；打开几秒内即可找到常用命令。本文件不记录 Groundary 当前运行状态；项目专属 npm scripts 只作为标注来源的实践示例，不作为通用速查内容。

所有需要填写的变量使用 `replace-with-*` 惰性占位符；复制代码块前先替换占位符，未替换时安全退出。具有状态变更、文件修改、安装、提交、推送或网络写入效果的操作，遵循「只读检查 → 人工暂停 → 独立变更」，不与检查用 `&&` 或连续无门控命令连接。

## 环境与入口

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“环境与入口”全部表格行。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `环境与入口`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: `codex` 与 `codebuddy` 的启动参数已按本机 `codex --help`、`codebuddy --help` 只读核验；`agent-a` 为当前工作流 agent 名，非通用标准。
</details>

示例中的 `$VARIABLE` 需先填写并核验；涉及状态变更（如 `apt` 更新）的命令执行前还应检查目标。

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `codex` | 启动 Codex CLI | Codex 在项目目录启动 | 先确认 `pwd` 和 `git status` |
| `codex --sandbox danger-full-access` | 以无沙箱限制、高风险模式启动 Codex | Codex 在项目目录启动 | 移除文件系统和网络沙箱边界；不是默认推荐模式，只在明确需要且环境可控时使用；不使用 bypass approvals 参数 |
| `codebuddy --agent agent-a` | 启动 CodeBuddy 并指定 Agent A | CodeBuddy 以 Agent A 身份启动 | `--agent` 已由 `codebuddy --help` 验证；`agent-a` 为当前工作流 agent 名 |
| `/exit` | 退出 Codex | 返回普通 Shell | 在 Codex 内输入 |
| `ssh "$SSH_TARGET"` | 登录服务器 | 出现服务器 Shell 提示符 | 先设 `SSH_TARGET="replace-with-user-at-host"` 并核验；在已配置 SSH Key 的终端执行；不记录临时公网 IP |
| `su - ubuntu` | 从 root 切换到项目用户 | `whoami` 输出 `ubuntu` | 当前为 root 时执行 |
| `cd -- "$PROJECT_DIR"` | 进入项目目录 | `pwd` 输出项目路径 | 先设 `PROJECT_DIR="/replace-with/project-dir"` 并核验；登录服务器后执行 |
| `http://127.0.0.1:replace-with-port` | 服务本机地址 | `/health` 返回 `healthy` | 在服务器本机访问；端口值先替换为实际端口 |
| `ssh -L "$PORT_FORWARD" "$SSH_TARGET"` | 建立本机到服务器 SSH 隧道 | 隧道会话保持连接 | 本机执行；先设 `PORT_FORWARD="replace-with-local-port:localhost:replace-with-remote-port"`、`SSH_TARGET="replace-with-user-at-host"`；浏览器访问 `http://localhost:replace-with-local-port` |
| `sudo apt update` | 更新软件包索引 | 命令成功完成 | 新服务器准备环境时执行 |
| `sudo apt upgrade -y` | 安装可用系统更新 | 升级完成且无失败 | 先 `sudo apt update`；关注是否提示重启 |
| `git --version` / `curl --version` | 确认基础工具可用 | 输出各自版本 | 部署前检查 |

## 常用 Shell

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“常用 Shell”全部表格行与说明。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `常用 Shell`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: 项目路径、进程标识、端口和凭据已移除；GNU Bash 手册覆盖语法与引用，但不覆盖块内每个外部工具。
</details>

示例中的 `$VARIABLE` 需先填写并核验；`kill` 等状态变更命令执行前还应检查目标进程。

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `pwd` | 显示当前目录 | 输出预期项目目录绝对路径 | 先确认是目标项目目录 |
| `ls` | 列出目录内容 | 出现预期文件和目录 | 敏感文件内容不要复制进文档 |
| `cd -- "$PROJECT_DIR"` | 切换目录 | `pwd` 显示目标目录 | 先设并核验 `PROJECT_DIR`（见「环境与入口」） |
| `whoami` | 显示当前用户 | 输出当前用户 | 确认用户具有任务所需的最小权限 |
| `rg -n "$SEARCH_PATTERN" "$SEARCH_PATH"` | 递归搜索文本 | 输出匹配文件、行号和内容 | 先填写并核验 `$SEARCH_PATTERN` 与 `$SEARCH_PATH`，避免扫描过宽 |
| `rg --files "$SEARCH_PATH"` | 快速列出文件 | 输出文件路径 | 先核验 `$SEARCH_PATH`；可按明确后缀过滤 |
| `grep -n "$SEARCH_PATTERN" "$TARGET_FILE"` | 搜索单个文件 | 输出匹配行 | 先核验 `$TARGET_FILE`；递归仓库搜索优先 `rg` |
| `test -f -- "$TARGET_FILE"` / `test -d -- "$TARGET_DIR"` | 判断文件/目录是否存在 | 存在时退出码为 0 | 变量含 `replace-with-*` 时先填写并核验路径 |
| `stat -- "$TARGET_FILE"` | 查看类型、权限与时间 | 不读取正文 | 先核验 `$TARGET_FILE`，避免指向敏感文件 |
| `ps` | 查看进程快照 | 识别目标进程和 PID | 用 `ps -ef` 再按用户/命令缩小范围 |
| `ss -ltnp` | 查看 TCP 监听 | 服务端口显示 `LISTEN` | 需要额外权限时用 `sudo` |
| `kill -- "$PID_TO_SIGNAL"` | 向精确 PID 发送信号 | 目标进程响应或退出 | 高风险：先设 `PID_TO_SIGNAL="replace-with-pid"`，核对 PID、用户和完整命令 |
| `mktemp` | 创建临时文件并输出路径 | 输出 `/tmp/...` 路径 | 用 `TMP_FILE="$(mktemp)"` 保存精确路径；只清理该路径 |
| `read -r -p "提示文字: " INPUT_VALUE` | 显示提示并读取输入 | 输入进入 `$INPUT_VALUE` | `-p` 后才是提示文字；输入值进入 `INPUT_VALUE` |
| `read -r -s -p "密码: " SECRET_VALUE` | 静默读取输入 | 输入进入 `$SECRET_VALUE`，终端不显示字符 | `-s` 表示输入不在终端回显，`-p` 后才是提示文字；静默输入不等于加密或安全存储；不要打印该变量值 |
| pipe `\|` | 把前一命令 stdout 交给后一命令 | 后一命令从 stdin 收到数据 | `set -o pipefail` 使管道失败能够传播 |
| `Ctrl + U` | 删除光标前的内容 | 当前命令行光标前被清空 | 输错长命令时使用 |
| `Ctrl + C` | 发送中断信号 | 多数交互程序返回 Shell | 只是中断信号，不保证所有程序退出 |
| `Ctrl + L` | 清屏 | 当前终端显示被清理 | 不会删除历史命令 |
| `↑` | 调出上一条历史命令 | 上一条命令出现在输入行 | 调出后检查再执行 |

`rm -rf`、`git reset --hard`、force push 和 bypass approvals 可能造成不可恢复损失，不作为日常速查命令使用。

静默读取的两行形式（`-s` 只关闭回显，不等于加密或安全存储；结束后手动换行）：

```bash
read -r -s -p "密码: " SECRET_VALUE
printf '\n'
```

## PM2

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块仅覆盖“PM2”全部表格行与说明。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `PM2`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: 进程名使用占位符；仓库没有 PM2 ecosystem 配置，首次启动参数不得猜测。
</details>

示例中的 `$PROCESS_NAME` 需先填写并核验；restart/stop 等状态变更命令执行前还应检查目标进程。

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `pm2 status` | 查看托管进程状态 | 目标进程为 `online` | `pm2 status` |
| `pm2 describe "$PROCESS_NAME"` | 查看进程描述与状态 | 输出目标进程详情 | 变更前只读确认 |
| `pm2 restart "$PROCESS_NAME"` | 重启服务 | 重启后回到 `online` | 高风险：先 `pm2 describe` 确认进程名 |
| `pm2 restart "$PROCESS_NAME" --update-env` | 重启并重新加载环境变量 | 状态 `online`，health 正常 | 修改 `.env` 后使用 |
| `pm2 stop "$PROCESS_NAME"` | 停止服务 | 状态变为 `stopped` | 高风险：仅在确实需要停服时执行 |
| `pm2 logs "$PROCESS_NAME"` | 持续查看服务日志 | 出现服务日志，无 Secret | 按 `Ctrl+C` 退出 |
| `online` / `stopped` / `errored` | 进程状态 | `online` 运行中；`stopped` 主动停服 | `errored` 先看 `pm2 logs`，不属于正常状态 |

## Git

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“Git”全部表格行、命令块与说明。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `Git`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: Git Reference — https://git-scm.com/docs
- Sensitivity: PUBLIC
- Notes: 分支名、远端名和 URL 已替换为占位符。
</details>

示例中的 `$VARIABLE` 需先填写并核验；add/commit/push 等状态变更命令执行前还应检查目标。

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `git status --short` | 紧凑显示工作区状态 | 收口后无输出 | `git status --short` |
| `git status` | 显示分支、暂存和未暂存状态 | 收口后为 `working tree clean` | `git status` |
| `git diff` | 查看未暂存修改 | 只出现本轮预期内容 | `git diff` |
| `git diff --check` | 检查空白和冲突标记问题 | 无输出且退出码为 0 | `git diff --check` |
| `git add -- "$FILE_TO_STAGE"` | 精确暂存指定文件 | `git status --short` 左列显示变更 | 只更新暂存区，不建立历史 |
| `git diff --cached` | 查看准备提交的内容 | 只有预期文件且无敏感信息 | `git diff --cached` |
| `git diff --cached --stat` | 查看暂存内容统计 | 文件列表和数量符合预期 | `git diff --cached --stat` |
| `git commit -m "$COMMIT_MESSAGE"` | 创建本地提交 | 输出短 Hash 和文件统计 | 确认 staged diff 后执行 |
| `git push "$REMOTE" "$BRANCH"` | 推送分支到远端 | 远端分支更新 | commit 后执行；先核对远端和分支；禁止 force push |
| `git log -1 --oneline` | 查看最新提交 | 显示预期 Hash 和消息 | `git log -1 --oneline` |
| `git rev-parse HEAD` | 输出本地当前完整 Hash | 与远端跟踪分支相同 | `git rev-parse HEAD` |
| `git fetch` | 更新远端引用，不合并 | 命令成功且工作区不被改写 | `git fetch` |
| `git pull` | 获取并合并当前分支 | 执行前工作区应 clean | `git pull` |
| `git clone "$REPOSITORY_URL"` | 克隆仓库 | 生成目录且下载完成 | 先设 `REPOSITORY_URL="replace-with-repository-url"`；clone 后核对 `git remote -v` |
| `git remote -v` | 查看远端地址 | 名称与 URL 正确 | `git remote -v` |

精确收口的最短顺序（`$REMOTE`、`$BRANCH`、`$FILE_TO_STAGE`、`$COMMIT_MESSAGE` 需先填写并核验）：

```bash
git status --short
git diff --check
git add -- "$FILE_TO_STAGE"
git diff --cached
git commit -m "$COMMIT_MESSAGE"
git push "$REMOTE" "$BRANCH"
git status
```

`git add` 只更新暂存区，`git commit` 才建立本地历史，`git push` 才更新远端；三个阶段不得合成一次粘贴执行。

## Node.js 与测试

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“Node.js 与测试”全部表格行与说明；项目专属脚本不在覆盖范围。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `Node.js 与测试`; `测试 / 本地验证命令`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: Node.js API documentation — https://nodejs.org/api/; npm Docs — https://docs.npmjs.com/
- Sensitivity: PUBLIC
- Notes: 项目专属 scripts 与文件路径已拆到“Groundary 实践示例”，未伪装成通用命令。
</details>

示例中的 `$TEST_FILE`、`$TARGET_FILE` 需先填写并核验。`npm test` 只运行当前项目 `package.json` 定义的 test script，不保证是 Node 内置测试或完整回归。

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `node --version` | 查看 Node.js 版本 | 输出 `v...` | `node --version` |
| `npm --version` | 查看 npm 版本 | 输出版本号 | `npm --version` |
| `npm install` | 安装当前项目 package.json 声明的依赖 | 依赖安装成功 | 会修改依赖目录，可能更新 lockfile |
| `npm ci` | 按 lockfile 安装 | 安装成功 | 需要 `package-lock.json` |
| `npm run` | 列出可用 scripts | 列出脚本 | 不猜测项目专属脚本 |
| `npm test` | 运行当前项目 package.json 定义的 test script | 以项目脚本和实际测试框架的成功条件为准；通常要求命令退出码为 0 | 不假设框架或覆盖范围；可能写文件或联网，先审阅 script |
| `node --test "$TEST_FILE"` | 运行指定测试文件 | 目标文件测试 `fail 0`（以实际报告为准） | 测试本身可能有副作用；先核验 `$TEST_FILE`，不猜测路径 |
| `node --check "$TARGET_FILE"` | 只检查 JavaScript 语法 | 无输出且退出码为 0 | 先核验 `$TARGET_FILE` 再执行 |
| `# tests N` / `# pass N` / `# fail 0` | `node --test` 默认报告的统计输出 | `pass N` 通常等于 `tests N`，`fail 0` | 非 0 时先停止收口并排查；其他框架输出可能不同 |

`npm test` 运行什么由当前项目 `package.json` 的 test script 决定：不保证使用 Node 内置测试、不保证覆盖全部回归测试，也不保证输出 `fail 0`；是否通过以项目脚本和所选测试框架为准。Groundary 的 `npm test` 行为见下方“Groundary 实践示例”的独立来源块。

### Groundary 实践示例

<details>
<summary>来源与验证状态（项目专属）</summary>

- Source scope: 本来源块仅覆盖“Groundary 实践示例”小节的 Groundary 两层 `package.json` 事实、`npm install`、`npm run install:proxy`、`npm start` 与安装顺序说明；不覆盖本文件其他通用 npm 语义。
- Knowledge type: PROJECT_SPECIFIC
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `package.json`; `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Groundary `package.json` 根 scripts（`install:proxy`、`start`）与两层 `package.json` 结构；`docs/DEVELOPMENT_CHEATSHEET.md` 中“新环境安装顺序”相关命令
- First practiced: UNKNOWN
- Last verified: 2026-09-03
- Technical authority: Groundary implementation evidence
- Sensitivity: PUBLIC
- Notes: 这些是 Groundary 项目事实，不是所有 Node.js 项目的通用做法；Groundary 仓库仍是运行权威，本手册只是教育性速查副本。
</details>

以下命令是 Groundary 项目事实，不是所有 Node.js 项目的通用做法；Groundary 仓库仍是运行权威，本手册只是教育性速查副本。完整测试脚本清单以 Groundary `package.json` 为准，更多脱敏案例见 [case-studies/groundary](../case-studies/groundary/README.md)。

新环境安装顺序（两层 `package.json`，只装子项目会漏装根依赖）：

```bash
npm install
npm run install:proxy
npm start
```

只执行 `npm run install:proxy` 会漏装 `backend` 直接引用的根项目依赖。项目专属测试脚本（如 `test:credentials`、`test:runtime-foundation` 等）是 Groundary 特有命令，不作为通用速查内容。

## Nginx

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“Nginx”全部表格行、配置示例与操作顺序。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `Nginx 公网测试部署`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: 部署路径、端口与服务身份已移除。
</details>

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `sudo nginx -t` | 检查 Nginx 配置语法与引用 | 显示 syntax / test successful | 每次修改配置后先执行 |
| `sudo systemctl reload nginx` | 平滑重载 Nginx 配置 | 命令成功，无配置错误 | 仅在 `nginx -t` 成功后执行 |
| `sudo systemctl status nginx` | 查看 Nginx 服务状态 | 显示 `active (running)` | reload 后或访问异常时检查 |

```nginx
root /replace-with-static-root;

location / {
    try_files $uri $uri/ =404;
}

location /api/ {
    proxy_pass http://127.0.0.1:replace-with-upstream-port;
}

location = /health {
    proxy_pass http://127.0.0.1:replace-with-upstream-port/health;
}
```

标准顺序：修改配置 → `sudo nginx -t` → 测试成功 → `sudo systemctl reload nginx` → `sudo systemctl status nginx`。静态页可达不证明后端可达。

## SQLite 与 Credential 安全检查

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“SQLite 与 Credential 安全检查”全部表格行、命令块与结尾说明。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`; `docs/LEARNING_KNOWLEDGE_BASE.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `SQLite 与 Credential 安全检查`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: SQLite PRAGMA documentation — https://sqlite.org/pragma.html
- Sensitivity: INTERNAL
- Notes: 只保留安全只读检查模式；不含数据库路径、Secret 标识、认证值或原始响应。
</details>

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `PRAGMA user_version` | SQLite schema 版本 | 输出版本号 | 使用下方只读命令 |
| `PRAGMA quick_check` | 快速完整性检查 | 输出 `ok` | 使用下方只读命令 |
| `PRAGMA foreign_keys` | 外键开关 | 输出 `0` 或 `1` | 只读检查 |
| `ls -l .env` | 查看环境文件权限 | mode 为 `-rw-------`（600） | 只检查权限，不读内容 |
| `git check-ignore -v "$TARGET_FILE"` | 确认文件不被 Git 跟踪 | 输出忽略规则 | 用于 `.env`、`*.db` 等敏感文件；先核验 `$TARGET_FILE` |

先使用惰性路径守卫，避免 `sqlite3` 因错误路径创建新文件：

```bash
DATABASE_FILE="/replace-with-database-file"
case "$DATABASE_FILE" in *replace-with-*) echo "请先填写 DATABASE_FILE"; exit 1 ;; esac
test -f "$DATABASE_FILE" || { echo "数据库文件不存在或不是普通文件"; exit 1; }
sqlite3 "$DATABASE_FILE" 'PRAGMA user_version;'
sqlite3 "$DATABASE_FILE" 'PRAGMA quick_check;'
sqlite3 "$DATABASE_FILE" 'PRAGMA foreign_keys;'
```

不要使用 `cat .env`；不要在终端、日志、文档或 Git 中输出 Master Key、Cookie/JWT、真实密码或真实 API Key。这些只读检查不证明备份、迁移、回滚或并发行为正确。

## curl 参数

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“curl 参数”全部表格行、示例与说明。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `curl 参数`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: curl command-line manual — https://curl.se/docs/manpage.html
- Sensitivity: PUBLIC
- Notes: 认证值与项目端点已移除；示例使用惰性占位符。
</details>

示例中的 `$URL`、`$PROXY_URL` 需先填写并核验；示例本地地址使用 `replace-with-*` 惰性值，未替换时命令应失败且不会请求真实服务。

| 参数/命令 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `curl "$URL"` | 发送基础 HTTP 请求，默认 GET | 返回目标 Response Body | 示例：`curl "http://127.0.0.1:replace-with-port/health"` |
| `-i` / `--include` | 同时显示响应头 | 能看到 HTTP status、Content-Type | `curl -i "$URL"` |
| `-b` / `--cookie` | 请求时携带 Cookie | 已登录 API 不再返回 401 | `-b "$COOKIE_JAR"` |
| `-c` / `--cookie-jar` | 保存服务器返回的 Cookie | Cookie 写入临时文件 | `-c "$COOKIE_JAR"` |
| `-H` / `--header` | 添加请求头 | 服务端按指定格式解析 | 不把认证材料写进历史或文档 |
| `-d` / `--data` | 发送请求体，通常自动 POST | 服务端收到 Body | `-d '{"example":"fake-value"}'` |
| `--data-binary @-` | 从 stdin 读取请求体 | pipe 输入被完整发送 | `printf '%s' "$BODY" \| curl --data-binary @- "$URL"` |
| `-X` / `--request` | 显式指定 HTTP Method | 请求使用指定 Method | `-X PUT`、`-X DELETE` |
| `-s` / `--silent` | 隐藏 progress meter | 输出更简洁 | `curl -s "$URL"` |
| `-S` / `--show-error` | silent 模式下仍显示错误 | 失败时仍有错误信息 | `curl -sS "$URL"` |
| `--fail-with-body` | HTTP 错误返回非零且保留响应体 | 失败时退出码非零 | 响应体仍可能敏感，不应直接提交 |
| `-o` / `--output` | 把响应体写入文件 | 文件包含响应 Body | 使用临时文件并按敏感级别处理 |
| `-w` / `--write-out` | 请求后输出状态等元数据 | 末尾出现 HTTP 状态码 | `-w '\n%{http_code}\n'` |
| `-L` / `--location` | 跟随重定向 | 自动访问重定向目标 | 跨主机重定向前检查认证头风险 |
| `-x` / `--proxy` | 使用 Proxy | 请求经过指定 Proxy | `curl -x "$PROXY_URL" "$URL"` |
| `--connect-timeout "$TIMEOUT_SECONDS"` | 限制连接建立时间 | 超时返回非零 | 与总超时不同 |
| `--max-time "$MAX_TIME_SECONDS"` | 限制整个请求时间 | 超时返回非零 | 示例：`--max-time 15` |

`-X` 大写指定 HTTP Method；`-x` 小写指定 Proxy。最短常用示例：

```bash
# Health GET（端口用 replace-with-* 惰性值，未替换时 curl 报错且不发送请求）
curl -i "http://127.0.0.1:replace-with-port/health"

# 使用惰性 Body 的 JSON POST
curl -i -H 'Content-Type: application/json' \
  -d '{"example":"fake-value"}' \
  "http://127.0.0.1:replace-with-port/api/example"
```

POST 可能修改服务状态；先确认健康检查输出、隔离环境和测试专用端点。

## HTTP status 与 API 字段

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“HTTP status 与 API 字段”全部表格行。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `HTTP status 与 API 字段`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: HTTP Semantics (RFC 9110) — https://www.rfc-editor.org/rfc/rfc9110.html
- Sensitivity: PUBLIC
- Notes: 项目专属 API 字段已移除；只保留通用状态码与通用响应字段。
</details>

| 状态/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| HTTP `200` | 请求成功 | Body 包含预期数据 | 同时检查应用层 `code` |
| HTTP `201` | 资源创建成功 | Body 返回新资源 metadata | 成功的 POST create |
| HTTP `400` | 请求格式或参数无效 | Body 给出可理解的错误 | 检查 Method、Header 和 Body |
| HTTP `401` | 未认证或登录 Cookie 无效 | 未登录出现属于正常保护 | 登录后重试 |
| HTTP `403` | 已认证但无操作权限 | 受限 mutation 被拒绝 | 不要通过换参数绕过权限 |
| HTTP `404` | 资源不存在或不属于当前用户 | 跨用户资源不泄露存在性 | 检查当前用户和资源 id |
| HTTP `429` | 请求频率或额度超限 | 返回限流提示 | 遵守服务端提示和有界重试 |
| HTTP `500` | 服务内部失败 | 返回受控错误 | 关联安全日志排查 |
| HTTP `502` | 网关收到无效上游响应 | 返回受控错误 | 区分代理与应用层故障 |
| HTTP `503` | 服务暂不可用 | 返回受控错误 | 检查健康状态和依赖 |
| HTTP `504` | 网关等待上游超时 | 返回受控错误 | 检查各层超时预算 |
| `code` | 应用层结果码 | 成功通常为 `0` | 与 HTTP status 一起判断 |
| `data` | 成功响应的业务数据 | 结构符合对应 API | 从 `data` 读取结果 |
| `error` | 稳定的应用错误标识 | 失败时为预期错误名 | 程序优先按它分支处理 |
| `message` | 供人阅读的结果说明 | 不包含 Secret 或内部堆栈 | 用于快速理解结果 |
| `id` | 资源标识 | 是当前用户可访问的资源 id | 后续 URL 使用返回值，不写死 |

## SSH Key

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块仅覆盖“SSH Key”全部表格行与说明。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `SSH Key`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: 不含密钥正文或主机身份。
</details>

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `ssh -V` | 查看 SSH 客户端版本 | 输出 OpenSSH 版本 | `ssh -V` |
| `ssh-keygen -t ed25519` | 创建 Ed25519 密钥对 | 生成私钥和 `.pub` 公钥 | `ssh-keygen -t ed25519 -C "设备名称"` |
| `id_ed25519` | SSH 私钥 | 只保存在受控设备，权限受限 | 禁止发送、上传或提交 Git |
| `id_ed25519.pub` | SSH 公钥 | 可添加到服务器授权列表 | 复制完整单行到 `authorized_keys` |
| `~/.ssh/authorized_keys` | 服务器允许登录的公钥列表 | 每行一把完整公钥 | 追加前先确认当前用户和文件 |
| `chmod 700 ~/.ssh` | 限制 SSH 目录权限 | 权限显示 `drwx------` | 在服务器对应用户下执行 |
| `chmod 600 ~/.ssh/authorized_keys` | 限制授权文件权限 | 权限显示 `-rw-------` | 在服务器对应用户下执行 |

私钥正文、可用配置和认证材料不得进入终端分享、日志、文档或 Git。

## 安全速查

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“安全速查”全部表格行与说明。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `安全速查`; `Evidence 与 Secret 安全规则（长期有效）`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: 只保留脱敏安全检查要点；不含 Secret 标识、认证值或原始响应。
</details>

| 字段/命令 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `.env` / Master Key / API Key | 必须留在受控环境的 Secret | Git diff 和 staged diff 中不存在 | push 前检查 `git diff --cached` |
| Cookie / JWT / 密码 | 用户认证敏感信息 | 文档、日志和 Git 中不存在真实值 | 使用临时文件、stdin 和静默输入 |
| fake Credential | 仅供自动测试的假数据 | 明确以 `fake` 标识且不进入生产 DB | 只在测试源码或测试命令使用 |
| `git diff --cached` | 提交前最后安全审阅 | 只有预期代码，无敏感值 | 每次 commit 前执行 |
| `working tree clean` | 没有未提交修改 | `git status` 明确显示 clean | 每次收口后确认 |

只检查 configured / missing 等布尔状态，不输出 Secret 正文；测试替身不得连接真实 Provider，也不得读取真实 Credential。

## Codex CLI

### 更新

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块仅覆盖“Codex CLI 更新”全部表格行与说明。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `Codex CLI 更新`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: OpenAI Codex CLI documentation — https://developers.openai.com/codex/cli
- Sensitivity: PUBLIC
- Notes: 包身份保持为用户变量；更新前确认实际安装方式。
</details>

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `command -v codex` | 定位 codex 可执行文件 | 输出路径 | 识别安装方式 |
| `codex --version` | 查看当前版本 | 输出版本号 | 更新前后对比 |
| `npm list -g --depth=0` | 查看全局 npm 包 | 确认是否 npm 管理 | 判断是否 npm-global 安装 |

按安装来源更新：先用 `command -v codex`、`codex --version`、`npm list -g --depth=0` 判断当前安装来源，再使用与原安装方式匹配的官方更新方法：独立安装器用官方更新脚本，npm 安装用对应 npm 包的官方更新命令。如果 Codex 由 Homebrew 或其他包管理器安装，应使用对应安装来源的官方更新方式。不虚构统一更新命令、不默认 `sudo`，也不升级 npm。

### 恢复会话

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块覆盖“恢复会话”全部表格行、命令块与说明。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: personal-engineering-handbook
- Source repository: OpenAI Developer Documentation (external official documentation) — <https://developers.openai.com/>
- Source document: https://developers.openai.com/codex/developer-commands#codex-resume
- Source commit: N/A (non-Git source)
- Source section: `codex resume`
- First practiced: 2026-09-02
- Last verified: 2026-09-02
- Technical authority: OpenAI Codex Developer Commands — https://developers.openai.com/codex/developer-commands#codex-resume
- Sensitivity: PUBLIC
- Notes: `Source commit` 不适用外部动态官方文档；`--last`/`--all` 已另按本机 `codex resume --help` 只读核验。未记录会话 ID、项目绝对路径、用户账号、终端输出、认证信息或额度数据。
</details>

| 命令/字段 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `codex resume --last` | 恢复当前目录最近一次会话 | 进入最近交互会话 | 跳过选择器；恢复后核对任务名称、目录和进度 |
| `codex resume` | 打开交互会话选择器 | 显示当前目录会话列表 | 多会话时用；核对时间、目录、任务名后选择 |
| `codex resume --all` | 扩大候选到所有目录会话 | 显示含工作目录列的会话列表 | 只是扩大候选，非“恢复全部”；更易选错，选中后重新核对 |
| `codex resume "$SESSION_REFERENCE"` | 指定会话恢复 | 进入指定会话 | 不在共享文档记录真实 ID；`$SESSION_REFERENCE` 填会话 ID 或名称 |

恢复后先检查工作树：

```bash
pwd
git status --short
git diff --check
git diff --cached --name-only
```

- 会话恢复不代表自动恢复未写盘内容、已退出 Shell 进程、Shell 环境变量或其他目录会话。
- 恢复后先核验目录、Git 状态、未暂存差异和暂存区；选错会话用 `/exit` 退出。
- 不要因会话中断立即运行 `restore`、`reset`、`checkout` 或 `clean`。

## tmux

<details>
<summary>来源与验证状态</summary>

- Source scope: 本来源块仅覆盖“tmux”全部表格行与说明。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `tmux（终端复用 / 持久终端会话）`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: 会话名已泛化。
</details>

示例中的 `$SESSION_NAME` 需先填写并核验；`kill-session` 等状态变更命令执行前还应检查目标会话。

| 命令/按键 | 含义 | 看到什么算正常 | 怎么用/注意事项 |
|---|---|---|---|
| `tmux new -s "$SESSION_NAME"` | 创建命名会话 | 进入 tmux，左下角显示 `[$SESSION_NAME]` | 开始长任务前执行 |
| `tmux ls` | 列出会话 | 显示会话列表 | 确认会话还在 |
| `Ctrl+B` 然后 `D` | 分离会话（不停止任务） | 回到普通 Shell，提示 `[detached]` | 临时离开时使用 |
| `tmux attach -t "$SESSION_NAME"` | 重新连接会话 | 回到会话，任务仍在 | SSH 断线重连后执行 |
| `tmux has-session -t "$SESSION_NAME"` | 检查会话是否存在 | 存在时退出码为 0 | kill 前只读确认 |
| `tmux kill-session -t "$SESSION_NAME"` | 结束命名会话 | 会话从 `tmux ls` 消失 | 确认任务已结束后执行 |

tmux 只用于交互式/临时的长任务；生产服务仍用 PM2 / systemd 托管，不靠 tmux 保活。

## 维护规则

只加入会重复使用的字段、命令、正常结果和最短用法；一次性流程、阶段记录、架构说明和测试报告放到其他项目文档。
