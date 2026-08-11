---
id: T7
title: the logging + security line
label: wayfinder:grilling
status: closed
assignee: claude (T7 session)
blocked_by: []
created: 2026-08-11
closed: 2026-08-11
---

## Question

client traffic flows THROUGH us now - the read-only story changes. do we store prompts or strip them, what do we keep for spend detection (per-request spend logs yes - payloads?), what do we tell designlilbro we can see, and what is marketing allowed to say while the PoC runs.

the jul 30 guardrail stands: positioning doesn't get ahead of the spike. and T1's security history belongs in how we talk about it - we're putting client traffic on software that was attacked this year, patched or not. the answer here writes the security paragraph of the build scope.

## Resolution

grilled with Ham, aug 11. four calls, all his:

- payloads: strip. metadata only - litellm's default. one spend row per request (tokens, cost, model, hashed key, team, cache hits, latency), the prompt/response columns in LiteLLM_SpendLogs stay empty, no logging callbacks that carry message content. the T2 caching fix reads cache-hit metadata, not prompt text - spend detection loses nothing. storing prompt content is a far bigger surface than the audit ever had (Ham) - the audit never held a prompt, the gateway doesn't start.
- what designlilbro hears: the honest version, in writing. a half-page data-handling note at onboarding -> traffic transits a gateway we run, here is exactly what we store (the metadata list), prompt/response text is never stored, and storage-off is one config line they can look at. they ack the note. it becomes the template for paid v1. draft it alongside the cutover ticket (T4).
- the vendor's bad year goes IN the note, volunteered not if-asked: march 2026 supply-chain attack, april 2026 auth CVEs, all fixed by 1.84 - followed by our controls: pin an exact version at or above 1.84 (1.96.x today), verify cosign-signed images, watch their security feed. their dev would find the march story on google anyway - better it comes from us with the controls attached.
- marketing: nothing public until the september gate. landing page and outbound untouched. shubham/emma get one internal paragraph so sales doesn't freelance; the whole approved vocabulary if a prospect asks is "we're piloting a gateway with one client, numbers end of september." the audit product keeps its read-only story - still true, the gateway is a separate opt-in pilot. the shift underneath (Ham): the agent has been "read-only, we just read your usage" - the router puts traffic through us, and marketing doesn't get ahead of that.

this writes the security paragraph of the build scope: metadata-only logging as config, the data-handling note as an onboarding asset, pin + cosign + feed-watch as ops line items, and the marketing freeze until the gate.
