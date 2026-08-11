# Dash-Router

decision map for the router PoC. litellm free core, one pilot (designlilbro), numbers to Zach end of september. this repo is the map, not the code - the build scope gets written when the tickets close, and the code comes after the scope.

## how to read it

- MAP.md -> the index. destination, standing rules, decisions so far, the fog, what's ruled out. start there.
- tickets/ -> one file per decision. status + blocked_by live in the frontmatter, closed tickets carry their resolution. the detail lives in the ticket, the map only gists it.
- research/ -> raw research with citations: litellm's free core + license, openrouter's fees + what's honestly measurable.
- assets/ -> things tickets produce. right now: the T3 card-run checklist (started as an ask for Bruno/TK, now Ham's own list - the openai half got answered first by the connected admin key).

## where it stands - aug 11

closed:

- what litellm's free core actually ships (T1) -> covers the whole PoC, MIT core resells clean, pin + verify signed images
- openrouter fees + what's honestly measurable (T2) -> BYOK free under $25k/month now; a provider-direct client means roughly zero day-one fee savings
- where it runs + who owns it (T5) -> isolated account, one VM, the compose stack; owner slot not a name, and the slot isn't Ham; self-serve base_url rollback, no SLA; security-only patching through the gate
- the logging + security line (T7) -> metadata only, prompts never stored; half-page data-handling note at onboarding, vendor's 2026 history included; marketing frozen until the gate
- does PoC data feed the audit (T8, revised same day) -> in. pilot logs pipe into the audit during the PoC as a separate reconciled source - per-request ground truth the providers can't give, and the only attribution once calls ride the proxy. joined on team/key, dollar truth stays provider APIs + invoices, recovery claims never touch pilot logs. detector rebuild waits until after the gate.

open:

- designlilbro's AI-spend picture (T3) -> half answered. openai is provider-direct with their own admin key connected read-only (991 buckets of real usage, by-key app split included). left: the card run, Ham himself this week - openrouter as a merchant yes/no is the answer that moves the map.

blocked on T3:

- day-one cutover for designlilbro (T4) - now also carries the T8 seam: keys/teams provisioned so the team/key join works from day one
- the gate number for Zach (T6)

## working a ticket

take a ticket that's open, unassigned, with nothing in blocked_by. put your name in assignee before you start. resolve the question, write the resolution into the ticket, flip status to closed, add the one-line gist to MAP.md's decisions so far. one ticket per session. the map never restates a decision - it points at the ticket that holds it.
