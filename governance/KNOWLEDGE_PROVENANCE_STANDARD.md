# Knowledge Provenance Standard

## 目的与适用范围

本标准规定知识从何而来、由什么证据支持、何时经过核验以及由谁提供技术权威。它适用于本仓库中所有术语、命令说明、学习结论、案例、待学习条目以及其他可被读者当作知识使用的内容。

基础设施档案的 `CURRENT`、`HISTORICAL`、`PLANNED`、`UNVERIFIED` 是事实状态；本标准的 `Knowledge status` 是知识状态。两套状态各自保留，不能互相替代。基础设施事实被解释、总结或复用为知识时，也必须遵守本标准。

## 强制来源字段

每个知识单元必须包含下列字段。字段不适用时必须填写 `N/A` 并在 `Notes` 说明原因，不得留空。

| 字段 | 定义与填写规则 |
|---|---|
| `Knowledge type` | 知识的用途分类，只能是 `GENERAL`、`PROJECT_SPECIFIC`、`CASE_STUDY` 或 `LEARNING_BACKLOG`。 |
| `Knowledge status` | 知识的证据状态，只能是 `VERIFIED`、`DERIVED`、`HISTORICAL`、`PLANNED`、`UNVERIFIED` 或 `DEPRECATED`。 |
| `Origin project` | 产生实践、观察或问题的项目。不是从项目产生的知识填写 `N/A (not project-derived)` 并在 `Notes` 说明其来源性质。 |
| `Source repository` | 保存一手来源的仓库名称及稳定 URL；多来源时逐项列出。非仓库来源须明确写出资料载体，不得伪造仓库。 |
| `Source document` | 来源文档的仓库相对路径或外部资料的稳定 URL。即使迁移后源文件被删除也保留原路径。 |
| `Source commit` | 来源文件被采用时的完整 40 位 Git commit hash。不得使用分支名、标签或短哈希代替；非 Git 来源填写 `N/A (non-Git source)` 并在 `Notes` 解释。 |
| `Source section` | 可定位到原文的标题、锚点、行号范围或记录标识。 |
| `First practiced` | 首次有证据实践该知识的日期，使用 `YYYY-MM-DD`；仅有年月或未知时明确写出精度或 `UNKNOWN`。 |
| `Last verified` | 最近一次依据来源重新核验的日期，使用 `YYYY-MM-DD`；从未核验填写 `NEVER`，同时状态必须适配。 |
| `Technical authority` | 用于判断技术正确性的规范、官方文档、标准或项目权威文档；必须与 `Origin project` 分开记录。没有已确认权威来源时填写 `UNVERIFIED`。 |
| `Sensitivity` | 信息敏感级别：`PUBLIC`、`INTERNAL`、`PRIVATE-INFRASTRUCTURE` 或 `SECRET`。`SECRET` 只标识类别，禁止保存 Secret 正文。 |
| `Notes` | 适用边界、推导关系、例外、失效风险、非 Git 来源说明或其他追溯信息。没有补充事项时填写 `None`。 |

### Knowledge type

- `GENERAL`：不依赖单一项目即可理解和复用的知识；从项目提炼时仍须保留 `Origin project`。
- `PROJECT_SPECIFIC`：仅对指定项目成立的约定、事实或做法，必须指向该项目的权威文档。
- `CASE_STUDY`：保留实践背景、决策、结果和适用边界的脱敏案例。
- `LEARNING_BACKLOG`：尚待学习、验证或整理的问题，不得表达为已经成立的结论。

### Knowledge status

- `VERIFIED`：已按所列来源和技术权威核验，且 `Last verified` 有有效日期。
- `DERIVED`：由两个或更多来源分析、归纳或推导而成，必须列出所有参与推导的来源及各自作用。
- `HISTORICAL`：曾经成立，只用于历史理解，不表示当前仍成立。
- `PLANNED`：计划采用或研究，尚未完成或核验。
- `UNVERIFIED`：证据不足、来源待确认或需要重新核验。
- `DEPRECATED`：不再建议使用；必须说明替代项或停止使用的原因。

## 强制规则

1. 每条知识都必须有可定位的来源。
2. `Origin project` 表示知识产生的上下文，`Technical authority` 表示判断技术正确性的依据，两者必须分开，不得相互代替。
3. 从项目中提炼出的 `GENERAL` 知识仍须保留来源项目。
4. `PROJECT_SPECIFIC` 内容必须指向项目权威文档，并记录对应的来源提交和章节。
5. `DERIVED` 内容必须列出所有参与推导的来源，不得只列最终结论或其中一个来源。
6. `PLANNED` 和 `UNVERIFIED` 内容必须使用计划、假设、问题或待验证措辞，不得写成已验证事实。
7. 没有来源或来源无法定位的知识不得合入 `main`，属于提交和评审的阻断问题。
8. 同一章节可以共享一个来源块，但必须声明 `Source scope`，准确列出该来源块覆盖的标题、表格、列表项或段落；未被覆盖的知识单元必须有独立来源块。
9. Git 来源的 `Source commit` 必须使用完整 40 位 commit hash。
10. 知识迁移后，即使源文件被移动或删除，也不得删除或改写原 `Source document` 和 `Source commit`；在 `Notes` 增补新位置或删除状态。
11. 后续补充外部官方资料时，不得替换 `Origin project`；应把官方资料加入 `Technical authority`，并保留原来源记录。
12. Secret、数据库、用户数据、原始供应商响应、未脱敏日志或其他禁止内容，不得以来源追溯为由复制进知识库。只记录安全的路径、提交、脱敏摘要或访问受限说明，并遵守 [Security and Secrets](./SECURITY_AND_SECRETS.md)。

## 来源继承与多来源规则

来源默认只作用于紧随其后的一个知识单元。章节级共享必须显式提供 `Source scope`；不得使用“本章同上”等无法稳定定位的表述。子章节可以继承父章节来源块，但父块的 `Source scope` 必须逐项包含这些子章节，并且子章节不得引入父块未覆盖的新结论。

如果一个知识单元包含不同来源或不同提交的结论，应使用编号来源记录，并在正文或 `Notes` 中说明每个来源支持哪一部分。`DERIVED` 必须使用这种多来源结构。来源发生漂移时保留旧记录，以新的核验记录补充，不得静默替换历史出处。

## 合入与维护责任

- 作者或 Agent 在提交前检查每个新增或修改的知识单元是否具有全部字段、有效 `Source scope`、完整 Git hash、正确状态和安全内容。
- 评审发现无来源、来源字段缺失、Git hash 不完整、`DERIVED` 来源不全或 `PLANNED`/`UNVERIFIED` 被写成事实时，必须阻断合入 `main`。
- Knowledge Steward 负责定期发现来源漂移、失效链接、被移动或删除的源文档、过期核验日期以及来源继承范围失真；修复时保留历史来源。
- 技术权威的更新不自动改变来源项目或历史实践记录；需要重新核验后再更新 `Last verified` 和状态。

## 标准模板

模板中的尖括号占位符必须在合入知识时替换。模板本身不构成知识内容。

### 单条术语模板

```markdown
## <Term>

- Plain-language explanation: <explanation>
- Engineering definition: <definition>
- Example: <safe example>
- Related concepts: <links or terms>
- Knowledge type: <GENERAL | PROJECT_SPECIFIC | CASE_STUDY | LEARNING_BACKLOG>
- Knowledge status: <VERIFIED | DERIVED | HISTORICAL | PLANNED | UNVERIFIED | DEPRECATED>
- Origin project: <project or N/A (not project-derived)>
- Source repository: <repository name and stable URL>
- Source document: <repository-relative path or stable URL>
- Source commit: <40-character full Git commit hash or N/A (non-Git source)>
- Source section: <heading, anchor, lines, or record identifier>
- First practiced: <YYYY-MM-DD | UNKNOWN>
- Last verified: <YYYY-MM-DD | NEVER>
- Technical authority: <official specification/documentation or UNVERIFIED>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <scope, caveats, or None>
```

### 命令速查章节模板

```markdown
## <Command topic>

<!-- Source scope: <exact headings, table rows, or list items covered> -->

- Knowledge type: <GENERAL | PROJECT_SPECIFIC>
- Knowledge status: <VERIFIED | DERIVED | HISTORICAL | PLANNED | UNVERIFIED | DEPRECATED>
- Origin project: <project or N/A (not project-derived)>
- Source repository: <repository name and stable URL>
- Source document: <path or URL>
- Source commit: <40-character full Git commit hash or N/A (non-Git source)>
- Source section: <section>
- First practiced: <YYYY-MM-DD | UNKNOWN>
- Last verified: <YYYY-MM-DD | NEVER>
- Technical authority: <official command documentation>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <supported versions and boundaries>

### `<command with inert placeholders>`

- Purpose: <purpose>
- Preconditions: <preconditions>
- Expected result: <result>
- Safety risks: <risks and rollback>
```

### 学习知识模板

```markdown
## <Learning topic>

- Knowledge type: <GENERAL | PROJECT_SPECIFIC>
- Knowledge status: <VERIFIED | DERIVED | HISTORICAL | PLANNED | UNVERIFIED | DEPRECATED>
- Origin project: <project or N/A (not project-derived)>
- Source repository: <repository name and stable URL>
- Source document: <path or URL>
- Source commit: <40-character full Git commit hash or N/A (non-Git source)>
- Source section: <section>
- First practiced: <YYYY-MM-DD | UNKNOWN>
- Last verified: <YYYY-MM-DD | NEVER>
- Technical authority: <official specification/documentation or UNVERIFIED>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <assumptions, applicability, limitations>

### Question

<What was being learned?>

### Evidence and reasoning

<Evidence, experiment conditions, and reasoning. For DERIVED, map every conclusion to all numbered sources.>

### Conclusion and limits

<Conclusion, confidence, and boundaries without overstating evidence.>
```

### 案例模板

```markdown
## <Case title>

- Knowledge type: CASE_STUDY
- Knowledge status: <VERIFIED | DERIVED | HISTORICAL | PLANNED | UNVERIFIED | DEPRECATED>
- Origin project: <project>
- Source repository: <repository name and stable URL>
- Source document: <project-authoritative document path>
- Source commit: <40-character full Git commit hash>
- Source section: <section>
- First practiced: <YYYY-MM-DD | UNKNOWN>
- Last verified: <YYYY-MM-DD | NEVER>
- Technical authority: <official specification/documentation or project authority>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <redaction, scope, or None>

### Context

<Sanitized context and constraints>

### Decision and evidence

<Decision, alternatives, and traceable evidence>

### Result and reusable lesson

<Observed result, limitations, and reusable lesson>
```

### 待学习条目模板

```markdown
## <Question to learn>

- Knowledge type: LEARNING_BACKLOG
- Knowledge status: <PLANNED | UNVERIFIED>
- Origin project: <project or N/A (not project-derived)>
- Source repository: <repository name and stable URL, or source carrier>
- Source document: <path or stable URL>
- Source commit: <40-character full Git commit hash or N/A (non-Git source)>
- Source section: <section that raised the question>
- First practiced: UNKNOWN
- Last verified: NEVER
- Technical authority: <candidate official source or UNVERIFIED>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <why it matters and what evidence is needed>

- Question: <question, not a factual claim>
- Verification plan: <steps and acceptance criteria>
```

### 多项目来源模板

```markdown
## <Derived knowledge title>

- Knowledge type: <GENERAL | CASE_STUDY>
- Knowledge status: DERIVED
- Origin project: <project A>; <project B>; <all other contributing projects>
- First practiced: <YYYY-MM-DD | UNKNOWN>
- Last verified: <YYYY-MM-DD>
- Technical authority: <official sources, kept separate from origin projects>
- Sensitivity: <PUBLIC | INTERNAL | PRIVATE-INFRASTRUCTURE | SECRET>
- Notes: <derivation method and applicability>

### Source 1

- Source repository: <repository A and stable URL>
- Source document: <path A>
- Source commit: <40-character full Git commit hash A>
- Source section: <section A>
- Contribution: <what this source supports>

### Source 2

- Source repository: <repository B and stable URL>
- Source document: <path B>
- Source commit: <40-character full Git commit hash B>
- Source section: <section B>
- Contribution: <what this source supports>

<!-- Repeat a numbered source block for every source used in the derivation. -->
```
