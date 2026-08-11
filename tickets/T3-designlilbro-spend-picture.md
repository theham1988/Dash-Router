---
id: T3
title: designlilbro's AI-spend picture
label: wayfinder:task
status: open
assignee: Ham
blocked_by: []
blocks: [T4, T6]
created: 2026-08-11
---

## Question

the pilot is designlilbro. before the cutover and the gate number can be decided we need their actual spend picture: which providers they pay and how (direct? openrouter? credits or BYOK?), monthly $ by merchant from our own card data, which apps or keys drive the spend, and who on their side holds the provider keys.

checklist for Bruno/TK - we see their card natively, this is a query not an integration. if openrouter shows up in the merchants: pull their activity export the same week, the analytics window only looks back 30 days (see T2).

why it matters: T2 says a provider-direct client yields roughly zero day-one fee savings. whether designlilbro has openrouter credits spend decides what number the gate can honestly show.

## Progress

- 2026-08-11: claimed (Ham). tried the AFK route first - the Metabase we can reach is the BlokID audit warehouse (no card ledger), the connected Slack is vauditpartners (Bruno/TK live on the dash.fi workspace), Drive has no pool sheet. so this resolves through Bruno/TK as the ticket guessed. the precise ask, paste-ready: [assets/T3-ask-for-bruno.md](../assets/T3-ask-for-bruno.md).
