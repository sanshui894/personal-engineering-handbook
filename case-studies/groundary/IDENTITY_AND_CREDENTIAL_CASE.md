# Identity and Credential Case

## Context

Groundary added multi-user identity, encrypted User Credentials, Managed and BYOK resolution, and external data/model Provider boundaries. A request had to carry trusted user identity through routes, storage and Provider context without exposing credential material.

## Problem

Authentication alone cannot stop user A from accessing user B's resource. A global credential pool can also mix ownership, mutable rotation and usage statistics. Storing recoverable API credentials adds a second problem: plaintext storage is unsafe, but one-way Hash cannot support later Provider calls.

## Constraints

- Frontend-supplied user IDs are untrusted.
- User resource queries and mutations must bind both resource ID and authenticated user ID.
- User Credential failure must not fall back to another user or silently consume System Credential.
- Routes and clients must not receive plaintext, ciphertext, IV or Authentication Tag.
- Provider identities appear only as `<DATA_PROVIDER>` and `<LLM_PROVIDER>`.

## Decision

Separate identity propagation, resource ownership, encrypted storage, credential resolution and Provider execution. Use authenticated encryption for recoverable credentials, keep Master Key outside storage, and construct isolated User Provider Contexts from trusted server-side identity.

## Implementation

Authentication middleware established trusted user context; storage CRUD bound user ID in SQL; Credential Service encrypted secrets and returned masked metadata; Resolver applied explicit Managed/BYOK choice; User contexts used isolated non-persistent provider state rather than the System pool. Missing, deleted, invalid or unavailable BYOK credentials failed closed.

## Verification evidence

The fixed source includes migration, crypto, storage, API, ownership and resolution tests. The learning source documents random IV, tamper/error-key failure, cross-user denial, no BYOK-to-Managed fallback and update/delete invalidation. Project context records the approved identity chain and data ownership model at that commit.

## What was project-specific

Groundary route shapes, schema names, task types, key-pool implementation and Provider adapters are project-specific. The exact runtime authority remains in Groundary code and project documentation.

## Reusable principle

Credential architecture has three orthogonal questions: who owns the secret, what external purpose it serves, and which trusted execution may use it. Keep those decisions explicit and separately testable.

## Limitations

Application-level encryption does not protect against simultaneous compromise of storage and Master Key. This case does not establish production key rotation, hardware-backed storage, session revocation or a public BYOK user interface.

## Follow-up

Key rotation, revocation, cache invalidation under scale and operational recovery require separate designs and tests. Until attached to evidence, they remain `PLANNED` / `UNVERIFIED`.

## Provenance

- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 3 — `backend/services/credentials/credentialCrypto.js`; Source 4 — `backend/services/credentials/credentialResolver.js`; Source 5 — `backend/storage/credentialStorage.js`; Source 6 — `backend/storage/migrations/002-user-credentials.js`; Source 7 — `backend/tests/credential-foundation.test.js`; Source 8 — `backend/tests/mcp-credential-resolution.test.js`
- Source commit: Sources 1–8 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Identity & Security Model`, `Data Ownership Model`, `Current Architecture`; Source 2 — `Credential` through `Credential Resolver`; Sources 3–6 — crypto, resolver, storage and schema implementations; Sources 7–8 — credential and resolution tests
- First practiced: UNKNOWN
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all listed sources. No credential, authentication value, database, raw response, or environment secret name is copied.
