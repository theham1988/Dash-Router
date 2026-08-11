---
id: T8
title: does PoC data feed the audit
label: wayfinder:grilling
status: closed
assignee: hamza
blocked_by: []
created: 2026-08-11
closed: 2026-08-11
---

## Question

litellm writes per-request spend logs - exactly the per-request data the audit's counterfactuals want and daily aggregates can't give. do we pipe pilot logs into the agent's detectors and briefing during the PoC, or keep the pipe standalone until after the gate?

free exports exist per T1 (s3, otel, prometheus). note team/key attribution is free but tag-based attribution is paid - that shapes how pilot data would join our schema. the narrative says audit findings feed the router someday - this ticket decides whether any of that wiring happens inside the PoC or stays out.

## Resolution

held at the door, but we keep the copy. decided with Ham 2026-08-11.

- no wiring inside the PoC. pilot rows don't feed the detectors or the briefing until after the gate. the gate is numbers, not integration - and the briefing surface is a v2.3/october thing anyway.
- but every pilot spend row lands in our own s3 bucket from day one. free config per T1, and worth doing regardless of litellm's own retention: the copy is ours even if the PoC box dies. post-gate wiring then starts with weeks of history instead of zero.
- one use inside the PoC window: the gate deck plans a hand-run exhibit - the audit's proxies next to real rows. actual off-hours requests instead of the weekend proxy, actual retry chains instead of the output-collapse proxy. analysis by hand off the s3 copy, no product wiring. noted in T6.
- grade discipline, standing: litellm's cost numbers are estimates (T2). whenever wiring does happen, pilot data is efficiency-grade evidence only - it never touches recovery claims, those stay invoice-backed.
- the join, when it comes: key/team attribution, which is free. tag-based attribution is paid - don't design the post-gate schema around tags.
- for T7: keeping a copy of spend rows in our bucket is part of what we tell designlilbro. payloads/prompts are a separate switch and stay T7's call.
- for the build scope: s3 export goes in the stand-up section; audit integration goes on the explicitly-not-in-the-PoC list.
