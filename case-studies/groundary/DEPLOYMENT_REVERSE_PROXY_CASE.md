# Deployment Reverse Proxy Case

## Context

During a temporary public deployment on `<CLOUD_PROVIDER>`, Groundary's static frontend was reachable while authentication features reported the backend unavailable.

## Problem

The visible page was served successfully, but requests to health and API paths were not forwarded to the application process. “The website opens” was mistakenly too weak an acceptance criterion for the full Web application chain.

## Constraints

- Source directory and static Web root were separate.
- The application listened on an internal port behind Nginx.
- Public test did not equal Production.
- Real addresses, absolute paths, ports, database files and cloud-specific identifiers could not enter the handbook.

## Decision

Use Nginx as a single public entry: serve static assets directly and reverse proxy `/health` plus `/api/*` to the internal application. Validate every layer separately.

## Implementation

The generalized chain was:

```text
Browser
  → <CLOUD_PROVIDER> network boundary
  → Nginx
      ├─ static frontend
      ├─ /health → application 127.0.0.1:<PORT>
      └─ /api/*  → application 127.0.0.1:<PORT> → SQLite
```

Configuration changes followed syntax check, reload, service status and endpoint verification.

## Verification evidence

The fixed project context records that public static access, health, authentication API and a controlled registration path were verified after proxy routing was added. It also labels the environment Public Test / Temporary and lists then-incomplete production capabilities as historical facts.

## What was project-specific

Groundary's route layout, application process, deployment topology and temporary cloud environment were project-specific. This copy removes all identifying infrastructure details.

## Reusable principle

Acceptance criteria must follow the request path end to end. Static content, proxy, application, authentication and persistence are separate checkpoints; success at an earlier layer cannot prove later layers.

## Limitations

This case does not establish HTTPS, domain, CI/CD, high availability, production hardening or present-day deployment state.

## Follow-up

Production readiness requires new evidence for TLS, domain, process recovery, backups, monitoring and deployment automation. Those items remain `PLANNED / UNVERIFIED` here.

## Provenance

- Knowledge type: CASE_STUDY
- Knowledge status: DERIVED
- Origin project: Groundary
- Source repository: Groundary — <https://github.com/sanshui894/Groundary>
- Source document: Source 1 — `docs/AI_PROJECT_CONTEXT.md`; Source 2 — `docs/LEARNING_KNOWLEDGE_BASE.md`; Source 3 — `docs/DEVELOPMENT_CHEATSHEET.md`
- Source commit: Sources 1–3 — `f3da01e331f5f2ecf7ce509c2deb0972e890548a`
- Source section: Source 1 — fixed-commit lines 1221-1244; Source 2 — `Web Deployment / Web 应用部署`; Source 3 — `Nginx 公网测试部署`
- First practiced: 2026-08-21
- Last verified: 2026-09-02
- Technical authority: Groundary implementation evidence; external authority not yet attached
- Sensitivity: INTERNAL
- Notes: Derived from all listed sources. Source 1 uses line numbers computed from the fixed commit to preserve traceability without restoring the cloud-provider heading. Cloud identity and infrastructure values are placeholders; this case does not maintain runtime authority.
