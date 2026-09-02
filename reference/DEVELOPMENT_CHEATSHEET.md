# Development Cheatsheet

本文件仅保存跨项目可复用的开发与运维命令。本文件不记录 Groundary 当前运行状态，也不包含项目专属 npm scripts。

所有需要填写的变量都使用包含 `replace-with-` 的明显惰性值。每个可复制代码块在使用变量前检查该标记；未替换时安全退出。具有状态变更、文件修改、安装、提交、推送或网络写入效果的操作必须采用“只读检查块 → 人工暂停 → 独立变更块”，不得把检查与变更用 `&&` 或连续无门控命令连接。命令文本不得放入变量后执行；不得使用 `eval` 或宽泛破坏性删除。

## Shell、文件、搜索与进程检查

### Provenance

- Source scope: 本来源块覆盖“Shell、文件、搜索与进程检查”下的“常用 Shell”“文件与目录检查”“文本搜索”“进程与端口”“临时文件、管道与退出码”全部表格、命令块和说明。
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
- Notes: GNU Bash documentation covers shell syntax and quoting but does not cover every external utility in this shared block. The block is therefore conservatively unverified. Project paths, process identifiers, ports, credentials, and destructive examples were removed.

### 常用 Shell

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
| `pwd` | 显示当前目录 | 应先确认输出是预期项目目录。 |
| `whoami` | 显示当前用户 | 确认用户具有任务所需的最小权限。 |
| `pwd` 后再使用本节的受守卫目录切换块 | 进入项目目录 | 不直接复制未核验路径。 |
| `ls -la` | 查看目录项与权限 | 隐藏文件也会显示；不要把输出中的敏感文件内容复制进文档。 |
| `command -v bash` | 查找 Bash 位置 | 成功时输出可执行文件路径。 |
| `bash --version` | 查看 Bash 版本 | 参数是否受支持取决于具体工具。 |

### 文件与目录检查

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
| `test -f -- "$FILE_PATH"` | 判断普通文件是否存在 | 先按下方模板定义并守卫 `FILE_PATH`。 |
| `test -d -- "$DIRECTORY_PATH"` | 判断目录是否存在 | 先按下方模板定义并守卫 `DIRECTORY_PATH`。 |
| `stat -- "$FILE_PATH"` | 查看类型、权限与时间信息 | 不读取正文；变量必须先通过惰性值检查。 |
| `find "$PROJECT_DIR" -maxdepth 2 -type f` | 有界列出文件 | 变量必须先通过惰性值检查，避免扫描过宽。 |
| `wc -l -- "$FILE_PATH"` | 统计行数 | 变量必须先通过惰性值检查。 |
| `sed -n "${START_LINE},${END_LINE}p" "$FILE_PATH"` | 读取指定行段 | 路径变量必须先核验，且确认文件不含 Secret。 |

只读路径检查模板：

```bash
FILE_PATH="/replace-with-file-path"
case "$FILE_PATH" in
  *replace-with-*) echo "请先填写 FILE_PATH"; exit 1 ;;
esac
test -f -- "$FILE_PATH" || {
  echo "文件不存在或不是普通文件：$FILE_PATH"
  exit 1
}
stat -- "$FILE_PATH"
```

### 文本搜索

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
| `rg -n "$PATTERN" "$PROJECT_DIR"` | 递归搜索并显示行号 | 两个变量必须先按路径检查模板定义、守卫和核验。 |
| `rg --files "$PROJECT_DIR"` | 快速列出可搜索文件 | 目录必须先核验；可再按明确后缀过滤。 |
| `grep -n "$PATTERN" "$FILE_PATH"` | 搜索单个文件 | 文件必须先核验；递归仓库搜索优先 `rg`。 |
| `grep -RIn "$PATTERN" "$DIRECTORY_PATH"` | 递归搜索 | 目录必须先核验，避免扫描系统或敏感路径。 |

### 进程与端口

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
| `ps -ef` | 查看进程快照 | 再按用户或进程名缩小范围。 |
| `ps -ef` 后按预期用户和命令筛选 | 查找目标进程 | 匹配结果可能包含搜索命令自身。 |
| `ss -ltnp` | 查看 TCP 监听 | 需要额外权限时再使用 `sudo`。 |
| `ss -ltnp` 后人工定位预期端口 | 检查指定端口 | 不在速查表中预填可能真实存在的端口。 |

向进程发送终止信号前，先只读显示 PID、所属用户、完整命令、启动时间和状态：

```bash
PID_TO_INSPECT="replace-with-pid"
case "$PID_TO_INSPECT" in
  *replace-with-*) echo "请先填写 PID_TO_INSPECT"; exit 1 ;;
esac
ps -p "$PID_TO_INSPECT" -o pid=,user=,lstart=,stat=,args=
```

暂停：人工核对以上目标，确认无误后才执行下一块。

变更阶段必须重新填写并自动比较 PID、用户和完整命令模式；任一不匹配即退出：

```bash
PID_TO_SIGNAL="replace-with-pid"
EXPECTED_USER="replace-with-expected-user"
EXPECTED_COMMAND_PATTERN="replace-with-expected-command-pattern"
for VALUE in "$PID_TO_SIGNAL" "$EXPECTED_USER" "$EXPECTED_COMMAND_PATTERN"; do
  case "$VALUE" in
    *replace-with-*) echo "请先填写全部进程确认变量"; exit 1 ;;
  esac
done

ACTUAL_USER="$(ps -p "$PID_TO_SIGNAL" -o user= | sed -n '1{s/^[[:space:]]*//;s/[[:space:]]*$//;p;}')"
ACTUAL_COMMAND="$(ps -p "$PID_TO_SIGNAL" -o args= | sed -n '1p')"
test -n "$ACTUAL_USER" && test -n "$ACTUAL_COMMAND" || {
  echo "目标 PID 不存在或无法读取"
  exit 1
}
test "$ACTUAL_USER" = "$EXPECTED_USER" || {
  echo "所属用户不匹配"
  exit 1
}
case "$ACTUAL_COMMAND" in
  *"$EXPECTED_COMMAND_PATTERN"*) ;;
  *) echo "完整命令不匹配"; exit 1 ;;
esac
ps -p "$PID_TO_SIGNAL" -o pid=,user=,lstart=,stat=,args=
kill -- "$PID_TO_SIGNAL"
```

### 临时文件、管道与退出码

`mktemp` 会创建文件，因此不与只读检查命令混写。使用临时文件时，应在获准的操作流程中单独创建并保存其精确路径；清理也只针对该精确路径。

```bash
set -o pipefail
printf '%s\n' 'alpha' 'beta' | wc -l
PIPELINE_STATUS=$?
```

- 管道把前一命令的标准输出交给后一命令。
- 默认情况下 `$?` 只代表最后一个命令；`pipefail` 使管道中的失败能够传播。
- Bash 可用 `${PIPESTATUS[@]}` 查看刚完成管道各段状态，但读取后应立即保存，避免被后续命令覆盖。
- `0` 通常表示成功，非零表示不同类型失败；具体含义以目标命令为准。

## Git 日常操作与精确收口

### Provenance

- Source scope: 本来源块覆盖“Git 日常操作与精确收口”下的全部表格、命令块和说明。
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
- Notes: Branch names, remote names, URLs, and project file paths were replaced with placeholders.

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
| `git status --short` | 紧凑查看暂存与未暂存状态 | 干净工作区无输出。 |
| `git diff --name-only` | 查看未暂存文件范围 | 只应包含预期修改。 |
| `git diff --check` | 检查空白错误与冲突标记 | 通过时无输出且退出码为 0。 |
| `git diff --cached --name-only` | 查看暂存文件范围 | 提交前检查内容与敏感信息。 |
| `git diff --cached --stat` | 查看暂存统计 | 文件范围应与授权一致。 |
| `git log -1 --oneline` | 查看最近提交 | 显示短哈希和消息。 |
| `git rev-parse HEAD` | 获取完整当前提交哈希 | 用于精确来源或同步核对。 |
| `git remote -v` | 查看远端配置 | 核对远端名称与 URL。 |
| `git branch --show-current` | 查看当前分支 | 必须与另行填写的预期分支精确一致。 |

### 阶段 1：仓库、分支与未暂存差异检查

```bash
PROJECT_DIR="/replace-with-project-directory"
EXPECTED_BRANCH="replace-with-expected-branch"
for VALUE in "$PROJECT_DIR" "$EXPECTED_BRANCH"; do
  case "$VALUE" in
    *replace-with-*) echo "请先填写 PROJECT_DIR 和 EXPECTED_BRANCH"; exit 1 ;;
  esac
done
case "$PROJECT_DIR" in
  /*) ;;
  *) echo "PROJECT_DIR 必须是绝对路径"; exit 1 ;;
esac
test -d "$PROJECT_DIR" || {
  echo "目录不存在：$PROJECT_DIR"
  exit 1
}
cd -- "$PROJECT_DIR" || exit 1
test "$(git branch --show-current)" = "$EXPECTED_BRANCH" || {
  echo "当前分支与 EXPECTED_BRANCH 不一致"
  exit 1
}
git rev-parse --show-toplevel
git branch --show-current
git status --short
git diff --check
git diff --name-only
```

暂停：人工核对以上目标，确认无误后才执行下一块。

### 阶段 2：精确暂存

```bash
PROJECT_DIR="/replace-with-project-directory"
EXPECTED_BRANCH="replace-with-expected-branch"
FILE_TO_STAGE="replace-with-repository-relative-file"
for VALUE in "$PROJECT_DIR" "$EXPECTED_BRANCH" "$FILE_TO_STAGE"; do
  case "$VALUE" in
    *replace-with-*) echo "请先填写全部暂存变量"; exit 1 ;;
  esac
done
case "$PROJECT_DIR" in /*) ;; *) echo "PROJECT_DIR 必须是绝对路径"; exit 1 ;; esac
test -d "$PROJECT_DIR" || exit 1
cd -- "$PROJECT_DIR" || exit 1
test "$(git branch --show-current)" = "$EXPECTED_BRANCH" || exit 1
test -f "$FILE_TO_STAGE" || {
  echo "待暂存文件不存在或不是普通文件"
  exit 1
}
git add -- "$FILE_TO_STAGE"
```

### 阶段 3：查看暂存内容

```bash
PROJECT_DIR="/replace-with-project-directory"
EXPECTED_BRANCH="replace-with-expected-branch"
FILE_TO_REVIEW="replace-with-repository-relative-file"
for VALUE in "$PROJECT_DIR" "$EXPECTED_BRANCH" "$FILE_TO_REVIEW"; do
  case "$VALUE" in *replace-with-*) echo "请先填写全部暂存复核变量"; exit 1 ;; esac
done
case "$PROJECT_DIR" in /*) ;; *) exit 1 ;; esac
test -d "$PROJECT_DIR" || exit 1
cd -- "$PROJECT_DIR" || exit 1
test "$(git branch --show-current)" = "$EXPECTED_BRANCH" || exit 1
git diff --cached --check
git diff --cached --name-only
git diff --cached -- "$FILE_TO_REVIEW"
```

暂停：人工核对以上目标，确认无误后才执行下一块。

### 阶段 4：提交

```bash
PROJECT_DIR="/replace-with-project-directory"
EXPECTED_BRANCH="replace-with-expected-branch"
COMMIT_MESSAGE="replace-with-commit-message"
for VALUE in "$PROJECT_DIR" "$EXPECTED_BRANCH" "$COMMIT_MESSAGE"; do
  case "$VALUE" in
    *replace-with-*) echo "请先填写全部提交变量"; exit 1 ;;
  esac
done
case "$PROJECT_DIR" in /*) ;; *) echo "PROJECT_DIR 必须是绝对路径"; exit 1 ;; esac
test -d "$PROJECT_DIR" || exit 1
cd -- "$PROJECT_DIR" || exit 1
test "$(git branch --show-current)" = "$EXPECTED_BRANCH" || exit 1
test -n "$(git diff --cached --name-only)" || {
  echo "暂存区为空"
  exit 1
}
git diff --cached --check || exit 1
git commit -m "$COMMIT_MESSAGE"
```

### 阶段 5：核验新提交

```bash
PROJECT_DIR="/replace-with-project-directory"
EXPECTED_BRANCH="replace-with-expected-branch"
for VALUE in "$PROJECT_DIR" "$EXPECTED_BRANCH"; do
  case "$VALUE" in *replace-with-*) echo "请先填写提交核验变量"; exit 1 ;; esac
done
case "$PROJECT_DIR" in /*) ;; *) exit 1 ;; esac
test -d "$PROJECT_DIR" || exit 1
cd -- "$PROJECT_DIR" || exit 1
test "$(git branch --show-current)" = "$EXPECTED_BRANCH" || exit 1
git log -1 --oneline
git status --branch --short
```

暂停：人工核对以上目标，确认无误后才执行下一块。

### 阶段 6：远端与推送目标检查

```bash
PROJECT_DIR="/replace-with-project-directory"
EXPECTED_BRANCH="replace-with-expected-branch"
REMOTE_NAME="replace-with-remote-name"
for VALUE in "$PROJECT_DIR" "$EXPECTED_BRANCH" "$REMOTE_NAME"; do
  case "$VALUE" in
    *replace-with-*) echo "请先填写全部推送检查变量"; exit 1 ;;
  esac
done
case "$PROJECT_DIR" in /*) ;; *) echo "PROJECT_DIR 必须是绝对路径"; exit 1 ;; esac
test -d "$PROJECT_DIR" || exit 1
cd -- "$PROJECT_DIR" || exit 1
test "$(git branch --show-current)" = "$EXPECTED_BRANCH" || exit 1
git remote get-url "$REMOTE_NAME"
git status --branch --short
git log -1 --oneline
```

暂停：人工核对以上目标，确认无误后才执行下一块。

### 阶段 7：推送

```bash
PROJECT_DIR="/replace-with-project-directory"
EXPECTED_BRANCH="replace-with-expected-branch"
REMOTE_NAME="replace-with-remote-name"
for VALUE in "$PROJECT_DIR" "$EXPECTED_BRANCH" "$REMOTE_NAME"; do
  case "$VALUE" in
    *replace-with-*) echo "请先填写全部推送变量"; exit 1 ;;
  esac
done
case "$PROJECT_DIR" in /*) ;; *) echo "PROJECT_DIR 必须是绝对路径"; exit 1 ;; esac
test -d "$PROJECT_DIR" || exit 1
cd -- "$PROJECT_DIR" || exit 1
test "$(git branch --show-current)" = "$EXPECTED_BRANCH" || exit 1
git push "$REMOTE_NAME" "$EXPECTED_BRANCH"
```

`git add` 只更新暂存区，`git commit` 才建立本地历史，`git push` 才更新远端；不得把三个阶段合成一次粘贴执行。

## SSH Key 权限

### Provenance

- Source scope: 本来源块仅覆盖“SSH Key 权限”全部表格和说明。
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
- Notes: No key material or host identity is included.

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
| `ssh -V` | 查看客户端版本 | 输出客户端版本。 |
| 受控密钥生成流程 | 生成密钥对 | 会创建密钥文件；先确认输出路径，私钥只留在受控设备，公钥可分发。 |
| 下方权限核验与变更流程 | 限制 SSH 目录和授权文件权限 | 不直接操作用户默认目录；路径和所有者必须由用户明确填写。 |
| 受控 SSH 身份测试 | 测试 Git SSH 身份 | 先核对目标主机；不得打印、复制或提交私钥。 |

先核验目标存在、类型、规范路径和所有者：

```bash
SSH_DIR="/replace-with-ssh-directory"
AUTHORIZED_KEYS_FILE="/replace-with-authorized-keys-file"
EXPECTED_USER="replace-with-expected-user"
for VALUE in "$SSH_DIR" "$AUTHORIZED_KEYS_FILE" "$EXPECTED_USER"; do
  case "$VALUE" in *replace-with-*) echo "请先填写全部 SSH 核验变量"; exit 1 ;; esac
done
test -d "$SSH_DIR" || { echo "SSH_DIR 不存在或不是目录"; exit 1; }
test -f "$AUTHORIZED_KEYS_FILE" || { echo "授权文件不存在或不是普通文件"; exit 1; }
test "$(stat -c '%U' -- "$SSH_DIR")" = "$EXPECTED_USER" || exit 1
test "$(stat -c '%U' -- "$AUTHORIZED_KEYS_FILE")" = "$EXPECTED_USER" || exit 1
realpath -- "$SSH_DIR"
realpath -- "$AUTHORIZED_KEYS_FILE"
stat -- "$SSH_DIR" "$AUTHORIZED_KEYS_FILE"
```

暂停：人工核对以上目标，确认无误后才执行下一块。

变更阶段重新填写所有目标并再次核验：

```bash
SSH_DIR="/replace-with-ssh-directory"
AUTHORIZED_KEYS_FILE="/replace-with-authorized-keys-file"
EXPECTED_USER="replace-with-expected-user"
for VALUE in "$SSH_DIR" "$AUTHORIZED_KEYS_FILE" "$EXPECTED_USER"; do
  case "$VALUE" in *replace-with-*) echo "请先填写全部 SSH 变更变量"; exit 1 ;; esac
done
test -d "$SSH_DIR" || exit 1
test -f "$AUTHORIZED_KEYS_FILE" || exit 1
test "$(stat -c '%U' -- "$SSH_DIR")" = "$EXPECTED_USER" || exit 1
test "$(stat -c '%U' -- "$AUTHORIZED_KEYS_FILE")" = "$EXPECTED_USER" || exit 1
realpath -- "$SSH_DIR"
realpath -- "$AUTHORIZED_KEYS_FILE"
chmod 700 -- "$SSH_DIR"
chmod 600 -- "$AUTHORIZED_KEYS_FILE"
```

私钥正文、可用配置和认证材料不得进入终端分享、日志、文档或 Git。

## curl 与 HTTP

### Provenance

- Source scope: 本来源块覆盖“curl 与 HTTP”下的“常用参数”“安全示例”“常见 HTTP 状态码”全部内容。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `curl 参数`; `HTTP status 与 API 字段`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: curl command-line manual — https://curl.se/docs/manpage.html; HTTP Semantics (RFC 9110) — https://www.rfc-editor.org/rfc/rfc9110.html
- Sensitivity: PUBLIC
- Notes: Authentication values and project endpoints were removed; examples use inert placeholders.

### 常用参数

| 参数 | 作用 | 注意事项 |
|---|---|---|
| `-i` / `--include` | 显示响应头 | 可同时检查状态码与内容类型。 |
| `-sS` | 隐藏进度但保留错误 | 自动化检查常用。 |
| `--fail-with-body` | HTTP 错误返回非零且保留响应体 | 响应体仍可能敏感，不应直接提交。 |
| `-H` / `--header` | 添加请求头 | 不把认证材料写进历史或文档。 |
| `-d` / `--data` | 发送请求体 | 通常触发 POST。 |
| `--data-binary @-` | 从标准输入读取原始请求体 | 适合管道输入。 |
| `-X` / `--request` | 指定 HTTP 方法 | 大写 `-X` 与代理参数小写 `-x` 不同。 |
| `-o` / `--output` | 将响应体写入文件 | 使用临时文件并按敏感级别处理。 |
| `-w` / `--write-out` | 输出状态、耗时等元数据 | 例如 `-w '%{http_code}\n'`。 |
| `-L` / `--location` | 跟随重定向 | 跨主机重定向前检查认证头风险。 |
| `--connect-timeout 5` | 限制连接建立时间 | 与总超时不同。 |
| `--max-time 15` | 限制整个请求时间 | 超时返回非零。 |

### 安全示例

```bash
CONNECT_TIMEOUT_SECONDS="5"
MAX_TIME_SECONDS="15"
PORT="replace-with-isolated-test-port"
case "$PORT" in *replace-with-*) echo "请先填写隔离测试服务端口"; exit 1 ;; esac
curl --fail-with-body --silent --show-error \
  --connect-timeout "$CONNECT_TIMEOUT_SECONDS" --max-time "$MAX_TIME_SECONDS" \
  "http://127.0.0.1:${PORT}/health"
```

以上 GET 仅用于核验回环地址上的隔离测试服务。POST 可能修改服务状态；先确认健康检查输出、隔离环境和测试专用端点。

暂停：人工核对以上目标，确认无误后才执行下一块。

```bash
SAFE_JSON_BODY='{"example":"value"}'
PORT="replace-with-isolated-test-port"
TEST_POST_PATH="replace-with-isolated-test-endpoint"
for VALUE in "$PORT" "$TEST_POST_PATH"; do
  case "$VALUE" in *replace-with-*) echo "请先填写隔离测试 POST 目标"; exit 1 ;; esac
done
printf '%s' "$SAFE_JSON_BODY" | \
  curl --fail-with-body --silent --show-error \
    -H 'Content-Type: application/json' \
    --data-binary @- \
    "http://127.0.0.1:${PORT}/${TEST_POST_PATH}"
```

### 常见 HTTP 状态码

| 状态 | 通常含义 | 处理建议 |
|---|---|---|
| `200 OK` | 请求成功 | 仍需验证响应契约。 |
| `201 Created` | 资源创建成功 | 检查返回的位置或资源标识。 |
| `400 Bad Request` | 输入或格式无效 | 修正请求，不应盲目重试。 |
| `401 Unauthorized` | 未通过认证 | 重新建立合法认证上下文。 |
| `403 Forbidden` | 已识别但无权限 | 不得通过更换参数绕过。 |
| `404 Not Found` | 资源不可见或不存在 | 也可能用于隐藏跨用户资源存在性。 |
| `429 Too Many Requests` | 请求频率或额度超限 | 遵守服务端提示和有界重试策略。 |
| `500 Internal Server Error` | 服务内部失败 | 关联安全日志排查。 |
| `502 Bad Gateway` | 网关收到无效上游响应 | 区分代理与应用层故障。 |
| `503 Service Unavailable` | 服务暂不可用 | 检查健康状态和依赖。 |
| `504 Gateway Timeout` | 网关等待上游超时 | 检查各层超时预算。 |

## Node.js 与 npm

### Provenance

- Source scope: 本来源块覆盖“Node.js 与 npm”全部表格、命令块和说明。
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
- Notes: Project-specific scripts and file paths were omitted.

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
| `node --version` | 查看 Node.js 版本 | 输出 `v...`。 |
| `npm --version` | 查看 npm 版本 | 输出版本号。 |
| 依赖安装（见下方分块流程） | 按 manifest 或 lockfile 安装依赖 | 会修改依赖目录，并可能更新 lockfile。 |
| `npm run` | 列出可用 scripts | 不猜测项目专属脚本。 |
| 项目测试入口 | 执行项目定义的测试 | 可能写文件或联网；先审阅 script，再按获准流程单独运行。 |
| 指定 Node 测试文件 | 运行选定测试 | 测试本身可能有副作用；不得猜测路径。 |
| `node --check "$JAVASCRIPT_FILE"` | 只做 JavaScript 语法检查 | 先守卫并核验文件路径；通过时无输出。 |

依赖安装前先只读核验仓库和 manifest；随后只选择一种安装方式：

```bash
PROJECT_DIR="/replace-with-project-directory"
case "$PROJECT_DIR" in *replace-with-*) echo "请先填写 PROJECT_DIR"; exit 1 ;; esac
case "$PROJECT_DIR" in /*) ;; *) echo "PROJECT_DIR 必须是绝对路径"; exit 1 ;; esac
test -d "$PROJECT_DIR" || exit 1
cd -- "$PROJECT_DIR" || exit 1
test -f package.json || { echo "缺少 package.json"; exit 1; }
npm --version
npm run
git status --short
```

暂停：人工核对以上目标，确认无误后才执行下一块。

```bash
PROJECT_DIR="/replace-with-project-directory"
INSTALL_MODE="replace-with-npm-install-or-npm-ci"
for VALUE in "$PROJECT_DIR" "$INSTALL_MODE"; do
  case "$VALUE" in *replace-with-*) echo "请先填写安装变量"; exit 1 ;; esac
done
case "$PROJECT_DIR" in /*) ;; *) exit 1 ;; esac
test -d "$PROJECT_DIR" || exit 1
cd -- "$PROJECT_DIR" || exit 1
test -f package.json || exit 1
case "$INSTALL_MODE" in
  npm-install) npm install ;;
  npm-ci) test -f package-lock.json || exit 1; npm ci ;;
  *) echo "INSTALL_MODE 只允许 npm-install 或 npm-ci"; exit 1 ;;
esac
git status --short
```

在多 package 项目中，应分别确认根目录与子项目的 manifests；只安装某一层依赖不能证明其他层可运行。

## PM2

### Provenance

- Source scope: 本来源块仅覆盖“PM2”全部表格和说明。
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
- Notes: Process names and startup parameters are placeholders; do not infer missing ecosystem configuration.

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
检查块只查看目标描述和全局状态：

```bash
PROCESS_NAME="replace-with-pm2-process-name"
case "$PROCESS_NAME" in *replace-with-*) echo "请先填写 PROCESS_NAME"; exit 1 ;; esac
pm2 describe "$PROCESS_NAME"
pm2 status
```

暂停：人工核对以上目标，确认无误后才执行下一块。

以下变更块中的 `PM2_ACTION` 只允许 `restart` 或 `stop`；如需重载环境，仅在已核对环境变更后填写 `restart-update-env`：

```bash
PROCESS_NAME="replace-with-pm2-process-name"
PM2_ACTION="replace-with-restart-stop-or-restart-update-env"
for VALUE in "$PROCESS_NAME" "$PM2_ACTION"; do
  case "$VALUE" in *replace-with-*) echo "请先填写全部 PM2 变量"; exit 1 ;; esac
done
pm2 describe "$PROCESS_NAME" || exit 1
printf '请输入完整进程名以确认：'
IFS= read -r CONFIRMED_PROCESS_NAME
test "$CONFIRMED_PROCESS_NAME" = "$PROCESS_NAME" || { echo "确认值不一致"; exit 1; }
case "$PM2_ACTION" in
  restart) pm2 restart "$PROCESS_NAME" ;;
  stop) pm2 stop "$PROCESS_NAME" ;;
  restart-update-env) pm2 restart "$PROCESS_NAME" --update-env ;;
  *) echo "不允许的 PM2_ACTION"; exit 1 ;;
esac
```

首次启动参数必须来自项目权威文档或 ecosystem 配置，不得猜测。

## Nginx 检查与反向代理

### Provenance

- Source scope: 本来源块覆盖“Nginx 检查与反向代理”全部表格、配置示例和操作顺序。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`; `docs/LEARNING_KNOWLEDGE_BASE.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `Nginx 公网测试部署`; `Web Deployment / Web 应用部署`
- First practiced: 2026-08-21
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: PUBLIC
- Notes: Deployment paths, ports, service identities, cloud platform, and production claims were removed.

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
先核验配置、服务存在性和当前状态：

```bash
SERVICE_NAME="replace-with-service-name"
case "$SERVICE_NAME" in *replace-with-*) echo "请先填写 SERVICE_NAME"; exit 1 ;; esac
sudo nginx -t
LOAD_STATE="$(systemctl show "$SERVICE_NAME" --property=LoadState --value)"
test -n "$LOAD_STATE" && test "$LOAD_STATE" != "not-found" || { echo "服务不存在"; exit 1; }
systemctl status "$SERVICE_NAME"
```

暂停：人工核对以上目标，确认无误后才执行下一块。

```bash
SERVICE_NAME="replace-with-service-name"
SYSTEMD_ACTION="replace-with-reload-or-restart"
for VALUE in "$SERVICE_NAME" "$SYSTEMD_ACTION"; do
  case "$VALUE" in *replace-with-*) echo "请先填写全部 systemd 变量"; exit 1 ;; esac
done
LOAD_STATE="$(systemctl show "$SERVICE_NAME" --property=LoadState --value)"
test -n "$LOAD_STATE" && test "$LOAD_STATE" != "not-found" || exit 1
systemctl status "$SERVICE_NAME"
printf '请输入完整服务名以确认：'
IFS= read -r CONFIRMED_SERVICE_NAME
test "$CONFIRMED_SERVICE_NAME" = "$SERVICE_NAME" || { echo "确认值不一致"; exit 1; }
case "$SYSTEMD_ACTION" in
  reload) sudo systemctl reload "$SERVICE_NAME" ;;
  restart) sudo systemctl restart "$SERVICE_NAME" ;;
  *) echo "只允许 reload 或 restart"; exit 1 ;;
esac
```

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

标准顺序：修改配置 → `nginx -t` → 核对目标服务 → 重载服务 → 检查服务状态 → 分别验证静态页、健康端点和 API。静态页可达不证明后端可达。

## tmux

### Provenance

- Source scope: 本来源块仅覆盖“tmux”全部表格和说明。
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
- Notes: Session names were generalized.

| 命令 / 按键 | 用途 | 注意事项 |
|---|---|---|
| `tmux list-sessions` | 列出现有会话 | 确认任务会话仍存在。 |
| `Ctrl+B` 后按 `D` | 分离会话 | 会话内进程继续运行。 |

结束会话前只读列出并显示精确目标：

```bash
SESSION_NAME="replace-with-tmux-session-name"
case "$SESSION_NAME" in *replace-with-*) echo "请先填写 SESSION_NAME"; exit 1 ;; esac
tmux list-sessions
tmux has-session -t "$SESSION_NAME"
```

暂停：人工核对以上目标，确认无误后才执行下一块。

```bash
SESSION_NAME="replace-with-tmux-session-name"
case "$SESSION_NAME" in *replace-with-*) echo "请先填写 SESSION_NAME"; exit 1 ;; esac
tmux has-session -t "$SESSION_NAME" || exit 1
printf '请输入完整 session 名以确认：'
IFS= read -r CONFIRMED_SESSION_NAME
test "$CONFIRMED_SESSION_NAME" = "$SESSION_NAME" || { echo "确认值不一致"; exit 1; }
tmux kill-session -t "$SESSION_NAME"
```

tmux 不是生产服务管理器；生产进程应由项目权威文档指定的正式托管机制管理。

## Codex CLI 更新

### Provenance

- Source scope: 本来源块仅覆盖“Codex CLI 更新”全部步骤和说明。
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
- Notes: Package identity remains a user-supplied variable; confirm the actual installation method before updating.

只读识别阶段：

```bash
command -v codex
codex --version
CODEX_PATH="$(command -v codex)" || exit 1
realpath -- "$CODEX_PATH"
npm list -g --depth=0
```

结合安装路径、包管理器记录和原始安装记录判断 Codex 是由独立安装器、npm 还是其他包管理器安装。使用与原安装方式匹配的官方更新方法；其他包管理器应使用其自身的官方流程。

### 独立安装器安装

仅在只读识别确认当前安装来自官方独立安装器后，才使用官方安装器更新。

暂停：人工核对以上目标，确认无误后才执行下一块。

```bash
INSTALL_METHOD="replace-with-confirmed-standalone-installer"
case "$INSTALL_METHOD" in *replace-with-*) echo "请确认独立安装器来源"; exit 1 ;; esac
test "$INSTALL_METHOD" = "standalone-installer" || exit 1
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

### npm 安装

仅在 `npm list -g --depth=0` 确认当前安装确实由 npm 管理后使用；不默认 `sudo`，也不升级 npm。

暂停：人工核对以上目标，确认无误后才执行下一块。

```bash
INSTALL_METHOD="replace-with-confirmed-npm-global"
CODEX_CLI_PACKAGE="replace-with-confirmed-codex-npm-package"
for VALUE in "$INSTALL_METHOD" "$CODEX_CLI_PACKAGE"; do
  case "$VALUE" in *replace-with-*) echo "请填写并确认 npm 安装信息"; exit 1 ;; esac
done
test "$INSTALL_METHOD" = "npm-global" || exit 1
npm list -g --depth=0 "$CODEX_CLI_PACKAGE" || exit 1
npm install -g "${CODEX_CLI_PACKAGE}@latest"
```

任一更新路径完成后重新只读核验：

```bash
command -v codex
codex --version
```

## Secret 卫生与 SQLite 只读检查

### Provenance

- Source scope: 本来源块仅覆盖“Secret 卫生”。
- Knowledge type: GENERAL
- Knowledge status: UNVERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `Evidence 与 Secret 安全规则（长期有效）`; `SQLite 与 Credential 安全检查`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: UNVERIFIED — project-derived; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Only safe inspection patterns are retained; no database path, secret identifier, authentication value, or raw response is included.

### Secret 卫生

- 不显示、复制或提交私钥、密码、认证令牌、会话值、API credential、环境文件、数据库或原始供应商响应。
- 示例值必须明显不可用；元数据只能说明 `configured` / `missing`、类型或安全掩码，不能还原正文。
- 提交前同时检查工作树和暂存区，确认没有敏感文件或值。
- 临时响应文件按敏感级别处理，检查后删除精确路径。
- 测试替身不得连接真实 Provider，也不得读取真实 Credential。

### SQLite PRAGMA 只读检查

#### Provenance

- Source scope: 本来源块仅覆盖“SQLite PRAGMA 只读检查”表格和结尾说明。
- Knowledge type: GENERAL
- Knowledge status: VERIFIED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: `docs/DEVELOPMENT_CHEATSHEET.md`; `docs/LEARNING_KNOWLEDGE_BASE.md`
- Source commit: `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: `SQLite 与 Credential 安全检查`; `SQLite Schema Migration`
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: SQLite PRAGMA documentation — https://sqlite.org/pragma.html
- Sensitivity: INTERNAL
- Notes: Only safe inspection patterns are retained; no database path or data content is included.

| 命令 | 用途 | 正常结果 / 注意事项 |
|---|---|---|
先使用惰性路径守卫，避免 `sqlite3` 因错误路径创建新文件：

```bash
DATABASE_FILE="/replace-with-database-file"
case "$DATABASE_FILE" in *replace-with-*) echo "请先填写 DATABASE_FILE"; exit 1 ;; esac
test -f "$DATABASE_FILE" || { echo "数据库文件不存在或不是普通文件"; exit 1; }
sqlite3 "$DATABASE_FILE" 'PRAGMA user_version;'
sqlite3 "$DATABASE_FILE" 'PRAGMA quick_check;'
sqlite3 "$DATABASE_FILE" 'PRAGMA foreign_keys;'
```

这些命令仅覆盖所列 PRAGMA 的只读检查，不证明备份、迁移、回滚或并发行为正确。
