---
id: T2
title: openrouter fees + what's honestly measurable
label: wayfinder:research
status: closed
assignee: claude (charting session)
blocked_by: []
created: 2026-08-11
closed: 2026-08-11
---

## Question

the money math for the gate: openrouter's current fees, what peeling a client to direct provider keys requires, and honestly - what does the gateway save on day one for a client who already pays providers directly.

## Resolution

full findings with sources -> ../research/openrouter-fees-measurement-2026-08-11.md

the short of it:

- fees today: 5.5% (min $0.80) on credit purchases. BYOK changed in august: free under $25k/month of list-price usage, 5% only above. so "kill the 5.5%" only exists for credits clients - a BYOK client under $25k/month already pays openrouter nothing. the options doc's fee story needs this update.
- stripe-openrouter: still reported talks (~$10B), no confirmed close as of aug 11.
- the honest list for a provider-direct client with no routing brain: recurring dollar savings on day one are roughly zero. same provider, same list price, plus a hop we now operate. real dollars in a 4-week window come from exactly two places -> anthropic prompt-caching fixes (cache_control on repeated prefixes, provider-verifiable) and cleanup the new per-key attribution enables (zombie keys killed, dev traffic downgraded). everything else - fallbacks, budget caps, key custody - is insurance. worth having, not bookable as savings.
- measurement mechanics for september: provider usage and cost APIs are the truth, litellm's own cost numbers are estimates - reconcile against invoices. compare per-unit costs and cached-token shares, not raw totals, because mix shift and seasonality corrupt before/after. and if any openrouter migration happens: their analytics only look back 30 days - export the baseline BEFORE moving traffic.
