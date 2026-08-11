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

in - piped during the PoC, as a separate reconciled source. Ham's call, 2026-08-11: the first take the same day held the logs at the door with an s3 copy; Ham pushed on it and the wiring is in.

- why in: per-request logs are data the audit cannot get today. providers only hand back daily aggregates - that's why our per-model numbers are estimates and why long-context is a blind spot. for pilot traffic, these logs make both ground truth.
- the seam that settled it (t4): once pilot calls ride the proxy, the provider api can't attribute them - they pile up under the proxy's upstream keys. attribution for piloted traffic comes from these logs, not the provider api. designlilbro is also the agent's first external beta - without the pipe, cutover would make the audit's view of them worse, not better.
- the shape: a separate source, reconciled - not blended in silently. per-request granularity from the logs; dollar truth stays provider cost apis + invoices (the t2 rule). joined on team/key - tag attribution is paid, nothing in our schema leans on tags.
- grade discipline unchanged: recovery claims stay invoice-backed and never touch pilot logs. detector/counterfactual use of per-request data is efficiency-grade evidence.
- what stays out of the PoC: rebuilding the counterfactual detectors around per-request data. that's flywheel work, after the gate. the pipe makes pilot numbers ground truth; it doesn't rewrite detectors mid-PoC.
- the s3 copy from day one stays - vendor-can-die rule, and it's the buffer the pipe reads from.
- the gate exhibit (noted in t6) runs off the piped source now - same proxies-vs-real exhibit, no hand-running.
- for t7: rows are ingested and retained - the designlilbro story covers ingestion + retention, not just transit. payloads/prompts stay t7's call.
- for the build scope: ingestion is a stand-up item - s3 export -> audit warehouse, team/key join, the reconciliation rule. "audit integration" comes off the explicitly-not list; "detectors rebuilt on per-request" goes on it.
