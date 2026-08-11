---
id: T5
title: where it runs + who owns it
label: wayfinder:grilling
status: closed
assignee: Ham
blocked_by: []
created: 2026-08-11
closed: 2026-08-11
---

## Question

where the proxy runs and who owns it. single instance + postgres is enough per T1 - but where, and on whose account. what uptime we owe designlilbro while their production calls flow through us, who's on call when it breaks at 2am, and which eng builds it with Piotr.

plus the ops policy T1's security history forces: pinned exact version (at or above 1.84, current 1.96.x), verified signed images, a patch cadence, and a named person watching their security feed. this software was attacked in march - the answer to "who owns it" includes owning that.

## Resolution (2026-08-11, grilled with Ham)

where: an isolated corner of our cloud. own account/project, one small always-on VM, the compose stack from T1 (litellm + postgres, no redis). client traffic and litellm's CVE history stay out of dash.fi prod's blast radius - if the box ever gets popped the attacker lands in an empty room, and it's the cleanest story to tell designlilbro. pick the region after [designlilbro's AI-spend picture](T3-designlilbro-spend-picture.md) shows where their calls come from.

who: no name on the map, on purpose. Ham is having someone else make that call, outside this effort. what the scope doc assumes is the shape: one owner end to end - builds it with Piotr, carries the pager, watches litellm's security feed. the build scope ships with an owner slot, not a name.

uptime: best effort, no SLA number. designlilbro gets a documented one-line rollback - flip base_url back to the provider - that they can run themselves at any hour without asking us. we fix the box when we're awake. litellm's own fallbacks don't cover the box itself dying, so the client-side flip is the only honest rescue - and for a free pilot whose day-one savings are roughly zero (T2), honest is the posture.

patching: freeze + security-only for the PoC window. pin the exact version (1.96.x today), verify the cosign signature on the image, move mid-pilot only for a security patch. the owner checks litellm's security feed weekly.

this writes the ops paragraph of the build scope. the rollback posture also feeds [day-one cutover for designlilbro](T4-day-one-cutover.md) - the self-serve flip is now a requirement of whatever cutover shape T3 allows.
