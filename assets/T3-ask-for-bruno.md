---
asset-for: T3
created: 2026-08-11
status: openai side closed via admin key (8/11); card run = Ham, week of aug 11
---

# the pull - designlilbro's AI spend from our card data

one query on the card ledger, no integration. window: feb 1 2026 to today, monthly, USD.

1. every AI merchant they paid on the card, $ by month, raw descriptor included. match at least: openai (split OPENAI *API vs OPENAI *CHATGPT), anthropic (split API vs CLAUDE.AI), openrouter, google cloud / gemini, cursor, perplexity, groq, together, replicate, elevenlabs, midjourney, xai, deepseek, mistral, fireworks, cohere - plus anything else that smells like an AI tool.
2. card names on each merchant. their virtual cards are usually named per app or team - that's our best proxy for which app drives the spend.
3. recurring vs one-off, and any annual-size charge.
4. the split that decides the gate: seats (chatgpt / claude subscriptions) vs API vs openrouter. seats never touch a proxy - only API and openrouter spend can move through the pilot.
5. if openrouter shows up at all: same-day ping. their analytics only look back 30 days, so the client needs to export activity the same week (T2).
6. gut check from whoever pulls it: does the AI merchant list look complete for them, or do they likely pay some providers off-card (ACH, invoicing)? the card only sees the card.
7. if their admin keys are already connected from the spend-agent beta onboarding, pull the by-key usage from the agent too - that's the real app split, better than card names.

not answerable from the card, goes to the designlilbro conversation (T4): who on their side holds the provider keys and owns the openai / anthropic orgs.

why this decides things: T2 closed on "provider-direct client = roughly zero day-one fee savings". whether designlilbro has openrouter credits spend decides what number the september gate can honestly show (T6), and how they pay today shapes the day-one cutover (T4).

# paste-ready ask for the dash.fi slack

Bruno / TK - for the router pilot: can you pull Designlilbro's card spend on AI merchants, Feb 1 to today, monthly by merchant, card names included? Same shape as the June pool pull, just one client. Two things inside it: split API vs ChatGPT/Claude subscriptions where the descriptor shows it, and if OpenRouter appears at all ping me same day - their export only looks back 30 days. Also a gut check: any chance they pay some AI providers off-card? :pray:

# update 2026-08-11, later

openai answered without the card: designlilbro has a live org admin key connected to the spend agent, read-only, 991 buckets of real direct usage. provider-direct, their key stays theirs, and the by-key usage gives the app split - item 7 above is done. Ham runs the card query himself this week, so the paste-ready ask is moot. items 1-6 are now his checklist, and the one answer that moves the map is item 5: openrouter as a merchant, yes or no.
