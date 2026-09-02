# Test Isolation Case

## Context

Groundary had a process-level usage-statistics component backed by the ignored local file `asin-proxy/.stats.json`. At the fixed source commit, the full test command still interacted with this state.

## Problem

Tests that reuse mutable runtime storage can change developer or operator state even when source control remains clean. An ignored file is not an isolated file: Git omission prevents commit, not mutation.

## Constraints

- Existing local statistics could not be guessed or rewritten safely.
- Test evidence had to distinguish source-tree cleanliness from runtime-state cleanliness.
- Production database content could not be copied or inspected for this knowledge migration.
- A suspected fixture failure statistic could not be promoted to a proven exact delta without stronger evidence.

## Decision

Record the pollution as an unresolved known issue, preserve the local file, avoid speculative restoration, and require a future isolation change to use a dedicated temporary stats target or injected persistence dependency.

## Implementation

No remediation was recorded in the fixed public project documents. The project context records that the test suite can update the ignored statistics file and that the run was suspected of adding a fixture-related failure statistic.

## Verification evidence

At the fixed commit, `.gitignore` excludes `asin-proxy/.stats.json`, and `docs/AI_PROJECT_CONTEXT.md` states that `npm test` updates it. The same source states that the production database was not modified. It does **not** establish exact counter changes, an internal write chain, or that stats isolation was fixed.

## What was project-specific

The relative stats path and test composition are Groundary-specific. The general lesson concerns any ignored mutable file shared by tests and runtime.

## Reusable principle

Test isolation must cover all mutable side effects—not only databases and network calls, but also ignored files, caches, counters, clocks and process-global state. Inject storage paths and use per-test temporary directories.

## Limitations

The exact pre-test and post-test stats contents were not preserved as authoritative evidence, so the suspected counter delta remains unverified. This case intentionally does not claim a root-cause fix. 为了保持脱敏和稳定定位，本教育案例没有引用供应商实现路径，因此不对内部写入函数作实现级断言。

## Follow-up

`PLANNED / UNVERIFIED`: introduce an injected stats store or temporary path, assert no write to the operational file, and add before/after isolation tests. Until those tests exist and pass, `.stats.json` pollution remains unresolved.

## Provenance

- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `.gitignore`
- Source commit: Sources 1–2 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `已知问题 — 测试统计文件隔离`; Source 2 — ignored stats-file rule
- First practiced: 2026-09-02
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from both listed, stable public project sources. Unresolved at the fixed source commit; production database was reported unmodified, while the exact stats delta and internal write chain remain unverified.
