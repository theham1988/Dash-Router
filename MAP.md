---
label: wayfinder:map
effort: router PoC
created: 2026-08-11
---

# router PoC - map to the build scope

## Destination

a build scope the engineers can start from - plain md in the project folder, same style as the v2.1 recovery doc. it says what we stand up, whose traffic goes through it, what we measure for the end-of-september gate, and what's explicitly not in the PoC. the map is done when every ticket is closed and the scope writes itself.

## Notes

- fixed at charting (aug 11, Ham): foundation = litellm free core. shape = PoC as the options doc framed it - free tier, one pilot, measure what it saves. pilot = designlilbro. gate = numbers to Zach end of september.
- standing rules from the options doc: no deal talk with the litellm company before the PoC. assume the vendor can die - keep our copy, keep an exit path. the brain is the prize later, this trip is the boring pipe. router stays off the roadmap until Ham moves it.
- options doc: https://app.notion.com/p/Router-Foundations-options-3ad23f46b9a280519f5ccb41bd719e2c (local: ../AI_Spend_Agent_Router_Options_2026-07-30.md)
- tracker: local markdown fallback - the charting session had no Linear or Notion write access. tickets live in tickets/, one file each, status + blocked_by in the frontmatter. a ticket is takeable when it's open, unassigned, and everything in blocked_by is closed. claim = put your name in assignee before working it. one ticket per session.
- docs in Ham's plain voice, no AI formatting. the research/ files are raw subagent output, left as-is for the citations.

## Decisions so far

- [what litellm's free core actually ships](tickets/T1-litellm-free-core.md) -> free core covers the whole PoC (virtual keys, budgets, spend logs, admin UI, fallbacks, openrouter upstream). MIT core, no restriction on running it for clients as a managed service. 2026 had a supply-chain attack + auth CVEs, all patched by 1.84 - pin current (1.96.x), verify signed images.
- [openrouter fees + what's honestly measurable](tickets/T2-openrouter-fees-measurement.md) -> credits fee 5.5% min $0.80. BYOK now free under $25k/month list price (changed aug 2026), 5% only above. for a client paying providers directly, day-one dollar savings are roughly zero without routing decisions - the gate number has to respect that.
- [does PoC data feed the audit](tickets/T8-poc-data-into-audit.md) -> in - piped during the PoC as a separate reconciled source (Ham pushed on the parked first take, same day). per-request logs = ground truth the providers can't give (per-model actuals, long-context), and once pilot calls ride the proxy they're the only attribution for piloted traffic - the t4 seam. joined on team/key, never tags. dollar truth stays provider apis + invoices; recovery claims never touch pilot logs; s3 copy stays; detector rebuild on per-request waits for after the gate.
- [where it runs + who owns it](tickets/T5-where-it-runs-who-owns-it.md) -> isolated account on our cloud, one always-on VM, the compose stack. owner unnamed on purpose - one person end to end (build + pager + security feed), name assigned off-map by whoever Ham hands it to. designlilbro gets no SLA but a self-serve base_url rollback they can run at any hour. version pinned + cosign-verified, security-patches-only through the gate.
- [the logging + security line](tickets/T7-logging-security-line.md) -> metadata only, prompts/responses never stored (litellm default - payload columns stay empty, no payload-carrying callbacks; the T8 s3 copy is spend rows, so it stays clean). designlilbro gets a half-page data-handling note at onboarding: what we keep, what we never keep, the vendor's 2026 history plus our controls (pin >=1.84, cosign-verified images, watch the feed). marketing says nothing public until the gate - one internal paragraph for sales.

## Not yet specified

- pilot onboarding mechanics - key provisioning, rollback plan, and drafting + handing over the T7 data-handling note. hangs on the day-one cutover ticket.
- whether the pilot sees a screen - litellm's admin UI as-is, something of ours, or nothing plus a report at the gate. hangs on the cutover shape.
- whether a second pilot (an openrouter-credits client) carries the fees-saved story if designlilbro turns out to be all provider-direct. hangs on their spend picture + the gate-number decision.
- what a paid v1 looks like if the numbers hold. stays foggy until the gate.

## Out of scope

- the routing brain (routellm / arch-router / ours trained on audit data). the prize, but a separate effort after the pipe is proven.
- the commercial conversation with the litellm company. opens with PoC numbers, not before.
- productization: multi-client onboarding, the enterprise tier (SSO past 5 users, audit logs, secret managers), our own UI layer, envoy migration thinking.
- roadmap placement. nothing from this map touches the roadmap until Ham moves it there.
