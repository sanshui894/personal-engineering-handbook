# Projection Bug Case

## Context

A Groundary controlled-real validation requested a product record for `<PRODUCT_ID>`. The `<DATA_PROVIDER>` call succeeded, but the projection layer returned unavailable Evidence, which then failed validation.

## Problem

The projection matched a requested identifier to a nested external record using raw strict string equality. The external presentation required approved canonical normalization, so a semantically matching record was lost even though the Provider response shape was otherwise usable.

## Constraints

- Raw Provider payload could not be persisted or logged.
- Identity matching could normalize presentation only; fuzzy matching was prohibited.
- Multiple canonical matches had to fail closed.
- Missing category semantics could not be inferred from an identifier.
- A Provider success could not be mislabeled as Evidence success.

## Decision

Run a sanitized structural diagnostic first, then remediate only the identity projection: canonical normalization, identity equality, exactly one canonical match, and fail closed on zero or multiple matches.

## Implementation

The diagnostic emitted safe structural metadata such as top-level type, candidate path, record count, canonical match count and correlation class. `recordForAsin()` was changed to compare canonical identities and require one unique match. Authoritative nested records remained intact, while a compatibility alias copied an existing label rather than inferring one.

## Verification evidence

The source records the transition from Provider success / projection unavailable / Evidence missing / validation invalid to Provider success / projected / Shared Evidence available / validation valid. Fixed source code contains canonical normalization and unique-match logic; tests verify normalization-only rather than fuzzy matching and fail-closed ambiguity.

## What was project-specific

The `<PRODUCT_ID>` format, product profile binding, evidence taxonomy, category fields and `<DATA_PROVIDER>` response presentation are Groundary-specific.

## Reusable principle

Presentation is not identity. When an external call succeeds but downstream Evidence is missing, inspect each boundary separately. Use sanitized shape diagnostics to localize the defect before changing schema, Provider code or business semantics.

## Limitations

The remediation proves the scoped product-binding path at the fixed checkpoint. It does not validate unrelated Provider tools, all identifier formats or fuzzy entity resolution. Deferred sales provenance and partial advertising fields remained separate gaps.

## Follow-up

Add canonical identity contract tests for each new Provider binding and preserve ambiguity tests. Any broader identity resolution remains `PLANNED / UNVERIFIED` until explicitly designed and validated.

## Provenance

- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 3 — `backend/runtime/orchestrator/providerBindings.js`; Source 4 — `backend/runtime/orchestrator/diagnostics/productProfileShapeDiagnostic.js`; Source 5 — `backend/tests/r4-p0-product-shape-diagnostic.test.js`; Source 6 — `backend/tests/r4-p0-sales-shape-diagnostic.test.js`
- Source commit: Sources 1–6 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `PRODUCT_BINDING_PROJECTION_BUG（root cause → CLOSED）`; Source 2 — fixed-commit lines 559-568; Sources 3–4 — canonical binding and sanitized diagnostic implementations; Sources 5–6 — structural diagnostic and normalization-only tests
- First practiced: 2026-08-27
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all listed sources. Source 2 uses line numbers computed from the fixed commit to preserve traceability without restoring the data-provider heading. Product identity and provider identity are placeholders; no raw response is copied.
