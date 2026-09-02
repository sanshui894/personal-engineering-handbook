# AI Workflow Governance Case

## Context

Groundary used several logical roles for product authority, architecture review, implementation, documentation stewardship and repository checkpointing. The project needed continuity across sessions without letting chat memory or one agent's wording become project fact.

## Problem

When planning, implementation, review, documentation and Git mutation share an unclear authority boundary, plans can be promoted to facts, agents can approve their own work, and concurrent edits can corrupt a shared worktree.

## Constraints

- Human authority remains explicit for goals, scope and high-impact real-world actions.
- Plan, recommendation, code presence and validated runtime result are distinct evidence states.
- One shared worktree uses sequential mutation by default.
- Documentation synchronizes reviewed facts; it does not create them.
- Git checkpoints require exact scope and independent readiness review.

## Decision

Define named roles with bounded inputs, outputs and mutation authority; route facts through an evidence authority chain; standardize handoff contracts; and separate documentation closeout from repository checkpoint approval.

## Implementation

The workflow established `USER_OPERATOR`, `AGENT_A`, `GPT_ARCHITECT_REVIEWER` and `CODEX_IMPLEMENTATION_AGENT`. It defined daily bootstrap, task brief, implementation closeout, reviewed facts, documentation closeout and independent `READY_FOR_COMMIT`. Context loading used task-scoped levels, while Fast Paths reduced ceremony without weakening authorization or evidence.

## Verification evidence

The governance document records the role matrix, evidence chain, handoff schemas, sequential mutation model and Git gate. The fixed project context separately records that Agent A's read-only steward workflow was validated while some hard-permission behavior remained unknown; it explicitly prohibits claiming full permission hardening.

## What was project-specific

Role names, the exact Agent A checkpoint gate, document hierarchy and daily handoff schemas are Groundary conventions. They are educational examples, not a universal organization chart.

## Reusable principle

Logical authority separation matters even when one tool performs several roles sequentially. Evidence should flow from execution to review to durable documentation, and mutation authority should be narrower than reasoning authority.

## Limitations

The case does not prove that every permission was technically enforced. Some boundaries were governance contracts rather than OS-level controls, and the fixed source explicitly leaves part of Agent A permission hardening unknown.

## Follow-up

Teams adopting the pattern should test actual tool permissions and worktree isolation rather than assuming written rules enforce themselves. Any concurrency model beyond sequential mutation remains `PLANNED` until isolated worktrees/branches and merge review are verified.

## Provenance

- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_WORKFLOW_GOVERNANCE.md`; Source 2 — `docs/AI_PROJECT_CONTEXT.md`; Source 3 — `docs/AGENT_A_INSTRUCTIONS.md`
- Source commit: Sources 1–3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — `Governing Principles`, `Roles and Decision Authority`, `Evidence Authority Chain`, `Standard Daily Workflow`, `Handoff Contracts`, `Repository Mutation Model`, `Git Governance`; Source 2 — `Multi-agent workflow / Agent A integration status`; Source 3 — Agent A operating contract
- First practiced: 2026-08-26
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all listed sources. Groundary governance remains authoritative for the project; this case does not grant permissions to agents in other repositories.
