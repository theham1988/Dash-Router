---
id: T8
title: does PoC data feed the audit
label: wayfinder:grilling
status: open
assignee:
blocked_by: []
created: 2026-08-11
---

## Question

litellm writes per-request spend logs - exactly the per-request data the audit's counterfactuals want and daily aggregates can't give. do we pipe pilot logs into the agent's detectors and briefing during the PoC, or keep the pipe standalone until after the gate?

free exports exist per T1 (s3, otel, prometheus). note team/key attribution is free but tag-based attribution is paid - that shapes how pilot data would join our schema. the narrative says audit findings feed the router someday - this ticket decides whether any of that wiring happens inside the PoC or stays out.
