# Brand Rename Case

## Context

Groundary adopted a new product brand while retaining several operational identifiers for compatibility. The change intentionally separated user-facing identity from paths, process names, session identifiers and storage names.

## Problem

A hard rename across every layer can cause deployment, session, automation and rollback failures. Conversely, leaving old branding everywhere makes the rename incomplete and confuses product identity with runtime compatibility.

## Constraints

- User-facing brand and repository metadata needed consistent updates.
- Existing deployments and sessions could depend on compatibility identifiers.
- Historical records had to retain the former name.
- Compatibility values could not be copied into this handbook.

## Decision

Use a soft rename: update presentation and package identity, preserve selected compatibility identifiers, document them as technical debt rather than current branding, and place a checkpoint before the rename for recovery.

## Implementation

The fixed source records updates to README, frontend presentation, package metadata, version comments and startup messages. It retained placeholders equivalent to `<COMPATIBILITY_COOKIE_NAME>`, `<COMPATIBILITY_PROCESS_NAME>`, `<COMPATIBILITY_PROJECT_PATH>`, `<COMPATIBILITY_DEPLOY_PATH>` and `<COMPATIBILITY_DATABASE_FILE>`.

## Verification evidence

The project context records passing targeted and full tests, clean diff check, successful process restart and health verification at the rename checkpoint. Package metadata and README in the fixed tree use the new brand. These facts apply to the fixed commit evidence, not an eternal current state.

## What was project-specific

Groundary, its former brand, exact files, version, remote strategy and compatibility identifiers are project-specific. The values are deliberately replaced with placeholders here.

## Reusable principle

Treat brand identity and runtime identity as separate migration layers. Rename presentation first when appropriate, preserve necessary compatibility values explicitly, test the whole chain, and maintain a later plan to retire legacy identifiers.

## Limitations

Soft rename leaves temporary naming debt and can confuse operators unless every compatibility value is labeled. This case does not prove those values were later retired.

## Follow-up

Each compatibility identifier should have an owner, dependency inventory, migration test and removal checkpoint. Retirement is `PLANNED / UNVERIFIED` until the source project records completion.

## Provenance

- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `README.md`; Source 3 — `package.json`; Source 4 — `backend/config/version.js`; Source 5 — `frontend/index.html`
- Source commit: Sources 1–5 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Brand Rename — Groundary（软改名）— 2026-09-02（CURRENT）`; Sources 2–5 — user-facing identity and package/runtime metadata at the fixed commit
- First practiced: 2026-09-02
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all listed sources. Compatibility values are placeholders; project source code and `docs/AI_PROJECT_CONTEXT.md` remain factual authorities.
