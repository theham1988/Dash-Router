---
id: T1
title: what litellm's free core actually ships
label: wayfinder:research
status: closed
assignee: claude (charting session)
blocked_by: []
created: 2026-08-11
closed: 2026-08-11
---

## Question

what does the free self-hosted core actually ship today - the features the PoC leans on (per-client keys, budgets, spend logs, admin screen, fallbacks, openrouter as upstream), what's gated in the paid tier, what it needs to run, and what the license says about running it for clients as a managed service.

## Resolution

full findings with sources -> ../research/litellm-free-core-2026-08-11.md

the short of it:

- free core covers the PoC end to end: virtual keys per app/client, budgets + rate limits per key/team/user, per-request spend logs + /spend endpoints, the admin UI, fallbacks/retries/load balancing, openrouter as an upstream provider, exports (s3, prometheus, otel, datadog). needs postgres - keys and spend live there. redis only when we run multiple instances, a single-instance PoC doesn't.
- the paid tier holds what bigger clients ask for: SSO past 5 users, audit logs, secret managers, key rotation, tag-based spend attribution, org-level spend. none of it blocks the PoC. note tag-based attribution is paid - team/key attribution is free, plan the data model around that.
- license: MIT on everything outside the enterprise/ directory. no managed-service restriction on the MIT core - the "can we resell it clean" spike item closes as YES, as long as no enterprise-directory features are enabled in production. not legal advice, but the license text is plain.
- security history matters: march 2026 supply-chain attack via compromised pypi releases (1.82.7/8), april 2026 auth CVEs including a SQL injection exploited in the wild within 36 hours. all fixed by 1.83.x/1.84. current stable is 1.96.x (aug 10). the scope must say: pin an exact version at or above 1.84, verify their cosign-signed images, watch their security feed. there's also an unverified class action naming the company - goes in the vendor-risk file, not a blocker.
- a rust rewrite of the hot path is rolling out behind an opt-in flag. keep it off for the PoC.
