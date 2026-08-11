---
id: T4
title: day-one cutover for designlilbro
label: wayfinder:grilling
status: open
assignee:
blocked_by: [T3]
created: 2026-08-11
---

## Question

what changes for designlilbro on day one: base-url swap on their existing setup vs new virtual keys per app. who holds the provider keys - theirs stay theirs behind our proxy, or ours. who has the conversation with them, and what's the rollback if the proxy misbehaves mid-pilot.

informed by [what litellm's free core actually ships](T1-litellm-free-core.md) - free core supports both shapes - and blocked on [designlilbro's AI-spend picture](T3-designlilbro-spend-picture.md) for how they hold keys today.

rollback posture landed in [where it runs + who owns it](T5-where-it-runs-who-owns-it.md): self-serve base_url flip, no SLA. this ticket works out the mechanics of that flip, not whether it exists.

from [does PoC data feed the audit](T8-poc-data-into-audit.md) (closed, revised): the seam - once pilot calls ride the proxy, the provider api can't tell designlilbro's traffic apart. attribution for piloted traffic comes from litellm's logs, joined on team/key. whatever cutover shape this ticket lands on has to provision keys/teams so that join works from day one.
