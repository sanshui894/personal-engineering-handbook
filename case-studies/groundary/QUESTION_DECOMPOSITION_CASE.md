# Question Decomposition Case

## Context

Groundary introduced `QUESTION_DECOMPOSITION_V0_3` to turn domain questions into a structured semantic representation before evidence planning. The design used a controlled vocabulary, strict parser, Golden cases, deterministic prompt identity and a governed `<LLM_PROVIDER>` run.

## Problem

Free-form model output can blur question type, subject, temporal scope, requested claim strength and ambiguity. Mapping richer meaning directly into an older intent model can silently weaken or discard user intent.

## Constraints

- LLM interprets intent; Platform owns evidence, tool and action authority.
- Requested analysis is not available Evidence.
- Unsupported mappings to the older intent model must fail closed.
- Question decomposition must make zero data-provider calls.
- One governed benchmark cannot establish general model accuracy.

## Decision

Create a versioned structured contract with explicit question types, subjects, temporal scope, subquestions, hypotheses and ambiguities. Validate duplicate-aware structure, use a bounded adapter to the existing intent model, and evaluate exact contract behavior with frozen cases and prompt fingerprinting.

## Implementation

The chain included a runtime-owned contract and parser, exact comparator, Mock benchmark, fail-closed adapter, R1-governed service, deterministic prompt, auditable result envelope and one explicitly authorized real benchmark runner. Temporary composite concepts stayed labeled temporary rather than permanent architecture.

## Verification evidence

The fixed source records local implementation and Golden normalization, plus exactly one governed real benchmark. It reports schema-valid and exact-match observations but explicitly forbids treating the exact-match fraction as general model accuracy. The bounded adapter rejected unsupported richer concepts as expected rather than compressing them.

## What was project-specific

Contract versions, field names, controlled vocabulary, Golden cases, Groundary's older intent schema and benchmark identities are project-specific. Model/provider identity is replaced with `<LLM_PROVIDER>`.

## Reusable principle

Preserve user meaning before capability fit. A semantic contract should expose ambiguity and requested claim strength, while downstream adapters reject unsupported meaning instead of silently weakening it. Evaluation identity should include the exact rendered prompt and contract version.

## Limitations

The case does not prove autonomous planning, evidence authority, tool authority, answer synthesis or broad semantic accuracy. Several field-level observations were preliminary, and the approved next analysis had not started at the fixed commit.

## Follow-up

Offline semantic error analysis was `PLANNED` at the source checkpoint. Prompt, Golden and Contract changes require separate authorization and evidence; no second benchmark or automatic rerun is implied.

## Provenance

- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `backend/runtime/question-decomposition/questionDecompositionContractV0_3.js`; Source 3 — `backend/runtime/question-decomposition/questionDecompositionParserV0_3.js`; Source 4 — `backend/runtime/question-decomposition/questionDecompositionPromptV0_3.js`; Source 5 — `backend/runtime/question-decomposition/questionDecompositionToIntentV0_1.js`; Source 6 — `backend/services/question-decomposition/questionDecompositionService.js`; Source 7 — `backend/benchmark/questionDecompositionBenchmarkV0_3.js`; Source 8 — `backend/tests/question-decomposition-golden-v0.3.test.js`; Source 9 — `backend/tests/question-decomposition-auditable-real-execution-v0.2.test.js`
- Source commit: Sources 1–9 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Post-Stage-6 Research — Question Decomposition V0.3 Controlled Runtime Closeout`; Sources 2–7 — contract, parser, prompt, adapter, service and benchmark implementations; Sources 8–9 — Golden and auditable execution tests
- First practiced: 2026-09-01
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all listed sources. Project source code and `docs/AI_PROJECT_CONTEXT.md` are factual authorities; benchmark output is summarized without raw artifacts.
