---
id: T5
title: where it runs + who owns it
label: wayfinder:grilling
status: open
assignee: Ham
blocked_by: []
created: 2026-08-11
---

## Question

where the proxy runs and who owns it. single instance + postgres is enough per T1 - but where, and on whose account. what uptime we owe designlilbro while their production calls flow through us, who's on call when it breaks at 2am, and which eng builds it with Piotr.

plus the ops policy T1's security history forces: pinned exact version (at or above 1.84, current 1.96.x), verified signed images, a patch cadence, and a named person watching their security feed. this software was attacked in march - the answer to "who owns it" includes owning that.
