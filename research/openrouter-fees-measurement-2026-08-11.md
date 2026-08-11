# OpenRouter fees + LiteLLM gateway PoC savings measurement — research findings (as of Aug 11, 2026)

> raw research-subagent output resolving ticket T2. left as delivered for the citations. sources inline.

## 1. OpenRouter fee schedule and Stripe-deal status

**Credit-purchase (platform) fee — current**
- Non-crypto (Stripe): **5.5% of order amount, $0.80 minimum**. Crypto (Coinbase): **5.0% flat, no minimum**. Source: https://openrouter.ai/docs/faq
- Structure set June 9, 2025, replacing the old ~5% + fixed $0.35 Stripe fee; blog example: $100 order → $5.50 fee. Source: https://openrouter.ai/blog/announcements/simplifying-our-platform-fee/
- No markup on inference itself: "We pass through the pricing of the underlying providers without any markup on inference pricing" — fee applies only at credit purchase. Source: https://openrouter.ai/docs/faq
- Pricing page shows the 5.5% as the pay-as-you-go "platform fee"; Enterprise: "Fee discounts available," invoicing, volume commitments; Free tier: 50 req/day. Source: https://openrouter.ai/pricing

**BYOK fee — current**
- 5% of "what the same model/provider would cost normally on OpenRouter," deducted from credits — but now only **above a plan allowance**: pay-as-you-go gets **$25,000/month of list-price inference with no fee**, Enterprise **$200,000/month**, 5% after. Sources: https://openrouter.ai/docs/use-cases/byok • https://openrouter.ai/pricing
- History: flat 5% on all BYOK usage → Oct 1, 2025: first 1,000,000 BYOK requests/month free, 5% beyond (https://openrouter.ai/blog/announcements/1-million-free-byok-requests-per-month/) → **August 2026: switched to the dollar-based plan allowance** (per the update note on that same post). Exact announcement date/post for the Aug 2026 change not located — UNVERIFIED (the $25k/$200k structure itself is verified on the live pricing and BYOK docs above).
- The June 2025 post floated replacing the 5% BYOK fee with "a fixed monthly subscription (pricing TBD)" (https://openrouter.ai/blog/announcements/simplifying-our-platform-fee/); current docs show the allowance model instead — the subscription never shipped as described.

**Volume tiers**
- FAQ: "OpenRouter does not currently offer volume discounts" (https://openrouter.ai/docs/faq) vs. pricing page Enterprise "Fee discounts available" + volume commitments (https://openrouter.ai/pricing). Read: no self-serve tiers; negotiated enterprise discounts exist. Tension between the two pages noted.

**Other 2026 changes (context, not fees)**
- Jan 29, 2026: Stripe named OpenRouter's payments/billing infrastructure partner (Invoicing, Tax, Radar). Source: https://stripe.com/newsroom/news/openrouter-and-stripe
- 2026 product announcements (no fee changes found in the announcements index): auto-router "Model Routing Powered by Wisdom of the Market" (Aug 10), Classifiers cost tracking (Jul 24), Guardrails (May 29), unified image API (Jun 23), $113M Series B (May 28). Source: https://openrouter.ai/blog/announcements/

**Stripe–OpenRouter acquisition status (as of Aug 11, 2026): still reported talks, no confirmed close**
- WSJ reported (Jul 23–24, 2026) Stripe in talks to acquire OpenRouter at ~**$10B**; "an agreement could be announced soon," but "talks may still fall apart or attract another buyer." Sources: https://finance.yahoo.com/technology/ai/articles/stripe-talks-acquire-openrouter-potential-215104525.html • https://www.axios.com/2026/07/24/stripe-openrouter-merger-ai-currency • https://www.pymnts.com/news/artificial-intelligence/2026/stripe-doubles-down-ai-with-openrouter-deal/
- Context: OpenRouter valued $1.3B in May 2026 (https://www.axios.com/2026/07/24/stripe-openrouter-merger-ai-currency); multiple suitors / preliminary offers reported Jul 18 (https://jawlah.co/en/60463, which also reports ~$50M annualized revenue by April 2026 — single-source, UNVERIFIED).
- No close/termination announcement found from Stripe newsroom, OpenRouter's blog (announcements through Aug 10 contain nothing), or major outlets as of today. One M&A aggregator states "Stripe has acquired OpenRouter for $10.0 billion" dated Jul 27, 2026, with zero cited sources (https://www.trysignalbase.com/news/acquisitions/openrouter-acquired-by-stripe-acquisition) — UNVERIFIED and contradicted by the absence of any primary-source confirmation. Treat the deal as rumored/pending.

## 2. Peeling a client off OpenRouter → direct keys behind self-hosted LiteLLM

**What it concretely requires**
- One provider account + billing relationship + API key set per provider (OpenAI org + Admin key from org settings: https://developers.openai.com/cookbook/examples/completions_usage_api; Anthropic org + admin key `sk-ant-admin01-...` for reporting: https://platform.claude.com/docs/en/manage-claude/usage-cost-api). Rate limits/quotas must be requested per provider — no shared pool.
- LiteLLM `config.yaml` `model_list`: map each public `model_name` → `litellm_params.model` (`openai/...`, `anthropic/...`), provider keys referenced as env vars server-side; multiple deployments can sit under one name for load balancing/failover. Source: https://docs.litellm.ai/docs/proxy/configs
- Model-name migration: OpenRouter IDs are vendor-prefixed slugs across **400+ LLMs** (https://openrouter.ai/models); every model string in client code must be remapped to the LiteLLM alias or the provider's native ID.
- Feature-parity regression testing: LiteLLM is an OpenAI-compatible gateway to 100+ providers with fallbacks, spend tracking, guardrails, prompt-caching support (https://docs.litellm.ai/docs/simple_proxy), and it translates prompt-caching (`cache_control` for Anthropic; auto for OpenAI; Gemini/Bedrock conversion) (https://docs.litellm.ai/docs/completion/prompt_caching) — but the top-level docs make no per-provider parity guarantee for streaming/tool-calls/structured output, so test those three paths per model before cutover. Flagged as a test item, not a known defect.

**What OpenRouter provides that direct keys lose**
- Automatic provider-layer failover ("on by default," 30-second health window) + opt-in model-fallback `models` array. Source: https://openrouter.ai/blog/insights/reliability-failover/ — LiteLLM replicates this pattern self-hosted (`fallbacks`, `cooldown_time`: https://docs.litellm.ai/docs/proxy/reliability) but you now operate it, and fallback breadth is limited to providers you hold keys for.
- One key / one credit pool / one invoice across 400+ models (https://openrouter.ai/models; billing via Stripe: https://stripe.com/newsroom/news/openrouter-and-stripe) → replaced by N provider invoices and N billing consoles.
- Sticky-session cache routing so multi-turn traffic re-hits the warm cache endpoint (https://openrouter.ai/blog/tutorials/prompt-caching-sticky-routing/) — moot with a single direct provider, relevant if you load-balance.
- Platform extras added in 2026: Guardrails, Classifiers, auto-router, unified image API, hosted analytics/export (https://openrouter.ai/blog/announcements/ • https://openrouter.ai/docs/guides/administration/activity-export).

## 3. Honest day-one value of LiteLLM for a client already paying OpenAI/Anthropic directly (no routing brain)

**Plain statement first: for a direct-payer client there is no OpenRouter fee to eliminate and no per-token price change — same provider, same list price — so a pass-through LiteLLM gateway produces approximately zero recurring dollar savings by itself on day one. It adds a hop and an ops cost. Direct dollars in a 4-week window come only from (a) caching configuration fixes and (b) cleanup actions the new attribution enables; everything else is insurance.**

| Item | Mechanism (source) | Dollar-measurable in ~4 weeks? |
|---|---|---|
| Cross-provider fallback during outages | `fallbacks`, retries, cooldowns (https://docs.litellm.ai/docs/proxy/reliability); provider outages are real and recurring (https://status.openai.com • https://statusgator.com/services/openai/outage-history) | No, unless an outage occurs in-window. And it saves downtime/revenue, not API spend — you still pay the fallback provider. Insurance-like. |
| Hard budget caps | `max_budget` + `budget_duration` per key/user/team; over-budget requests blocked with `ExceededTokenBudget` error (https://docs.litellm.ai/docs/proxy/users) | Only if a runaway would have happened; expect zero events in 4 weeks. Report as "overrun protection in place," not savings. Insurance-like. |
| Per-key/team/user/tag spend attribution | Spend logs with `api_key`, `team_id`, `request_tags`, `model_group`, `spend`; `/spend` + `/global/spend/report` endpoints (https://docs.litellm.ai/docs/proxy/cost_tracking) | Attribution itself saves $0. Dollars appear only when the client acts (kill zombie keys, downgrade dev traffic) — then yes, measurable as the retired workloads' run-rate. Credit the cleanup decision, not the proxy. |
| Centralized key custody | Apps get revocable virtual keys; real provider keys stay server-side in proxy config (https://docs.litellm.ai/docs/proxy/virtual_keys) | No. Leaked-key risk reduction; qualitative/insurance. |
| Prompt-caching configuration | LiteLLM passes/translates `cache_control`; `completion_cost()` accounts for cache discounts (https://docs.litellm.ai/docs/completion/prompt_caching). Anthropic: reads 0.1x input, writes 1.25x, explicit `cache_control` required; OpenAI: automatic ≥1,024 tokens at 0.25–0.5x (https://openrouter.ai/docs/guides/best-practices/prompt-caching) | **Yes — the one real dollars line**, IF the client has Anthropic traffic with repeated prefixes not currently using `cache_control`. Delta is provider-verifiable (cache token fields). Do NOT book OpenAI auto-caching — it happens without the gateway. |
| Proxy response cache (exact-match/semantic) | Redis/s3/in-memory exact-match, semantic options, TTL controls (https://docs.litellm.ai/docs/proxy/caching) | Yes in principle (cache hits × avoided cost, from spend logs), but only if genuinely duplicate full requests exist (eval suites, retry storms) — rare in production chat. Semantic cache trades answer quality; don't lead with it. |
| Retry/timeout hygiene + rate limits | `num_retries`, `request_timeout`, `allowed_fails`/cooldowns (https://docs.litellm.ai/docs/proxy/reliability); `tpm_limit`/`rpm_limit`/`max_parallel_requests` → 429 (https://docs.litellm.ai/docs/proxy/users) | No. Failed calls generally aren't billed, so retries improve success rate/latency, not spend; retries can slightly increase spend. Rate caps against runaway loops = insurance. |

- If the team needs one sentence for the pilot doc: **"Without routing/model-substitution decisions, the gateway's day-one dollar savings for a direct-payer client round to zero; the pilot's measurable dollars are caching fixes and attribution-driven cleanup, and the rest is risk control."**

## 4. Computing a credible "dollars saved" number for the end-of-September review

**(a) Client currently on OpenRouter → moved to direct keys via LiteLLM**
- Baseline sources (capture BEFORE migration — analytics window is short):
  - Activity export (CSV/PDF): spend, tokens, requests, broken down by model / API key / creator, at https://openrouter.ai/activity (https://openrouter.ai/docs/guides/administration/activity-export)
  - Analytics API: daily activity by model endpoint, **last 30 completed UTC days only**, requires a provisioning key (https://openrouter.ai/docs/api/api-reference/analytics/get-user-activity • https://openrouter.ai/docs/api-reference/analytics/get-activity); fields include `total_usage`, `usage_upstream`, `usage_cache`, `cache_hit_rate`, token/request counts (https://openrouter.ai/docs/cookbook/administration/analytics-cost-control)
  - Per-generation ground truth: `total_cost`, `cache_discount`, `native_tokens_prompt/completion`, `provider_name`, `is_byok`, `upstream_inference_cost` (https://openrouter.ai/docs/api/api-reference/generations/get-generation)
  - Credit-purchase receipts (Stripe) for actual fees paid.
- Formula (monthly): `saved = Σ credit-purchase fees avoided [5.5% × order, min $0.80 → https://openrouter.ai/blog/announcements/simplifying-our-platform-fee/] + BYOK fee avoided [5% × max(0, list-price BYOK usage − $25,000) → https://openrouter.ai/pricing] − gateway cost [hosting + eng-hours]`. Inference dollars are a wash by construction: OpenRouter passes through provider list price with no markup (https://openrouter.ai/docs/faq).
- Honesty caps: ceiling is ~5.5% of spend for a credits client (slightly higher if many small top-ups hit the $0.80 minimum). A BYOK client under $25k/month list price pays **$0** OpenRouter fee since the Aug 2026 change — nothing to save; say so if that's the case.

**(b) Client paying OpenAI/Anthropic directly (through LiteLLM pilot)**
- Baselines that exist independent of the gateway (audit-grade, provider-side):
  - OpenAI: Usage API `GET /v1/organization/usage/completions` (input/output/cached tokens, request counts; group/filter by project, user, API key, model; 1m/1h/1d buckets) + Costs API `GET /v1/organization/costs` (daily USD by line item and project; daily-only); Admin API key required (https://developers.openai.com/cookbook/examples/completions_usage_api; reference: https://platform.openai.com/docs/api-reference/usage/costs_object)
  - Anthropic: `GET /v1/organizations/usage_report/messages` (uncached/cached input, cache-creation, output tokens; group by API key, workspace, model, service tier; 1m/1h/1d, 1d capped at 31 buckets/page) + `GET /v1/organizations/cost_report` (daily USD, cents as decimal strings); admin key `sk-ant-admin01-...`; unavailable on individual accounts (https://platform.claude.com/docs/en/manage-claude/usage-cost-api)
  - Monthly invoices as the reconciliation truth for both.
- Defensible comparisons for the September review:
  - **Per-unit cost by model** ($/1M input, $/1M output, before vs after): should be flat — use it to prove the gateway added no cost, not that it saved money.
  - **Effective input cost after caching**: cache-read token share from provider usage fields, before vs after `cache_control` fixes; Anthropic savings ≈ Δcache-read tokens × 0.9 × input price − (cache-write tokens × 0.25 × input price) (multipliers: https://openrouter.ai/docs/guides/best-practices/prompt-caching). Provider-reported cached-token fields make this the strongest dollars claim.
  - **Avoided-overrun events**: count of budget-block errors × bounded estimate of what would have been spent (https://docs.litellm.ai/docs/proxy/users). Label "avoided risk," not savings; likely zero events.
  - **Attribution-actioned savings**: run-rate of workloads killed/downgraded after tag/key attribution (https://docs.litellm.ai/docs/proxy/cost_tracking), annualized from the post-action daily rate. Label as audit-driven, gateway-enabled.
- Pitfalls to pre-empt (write these into the review method now):
  - Usage-mix shift: new features, model upgrades, or agent loops between windows masquerade as savings/overruns — compare fixed-mix (hold baseline model/token mix constant and index) rather than raw totals.
  - Seasonality: an Aug→Sep window spans summer lull → back-to-work ramp; use same-length adjacent windows and per-unit metrics, not absolute spend.
  - Don't book OpenAI automatic caching (fires ≥1,024 tokens with no config: https://openrouter.ai/docs/guides/best-practices/prompt-caching) as gateway savings.
  - Reasoning/output token growth inflates spend independent of anything the gateway did.
  - LiteLLM's `completion_cost()` numbers are estimates and `user`-param attribution can be self-misdeclared (https://docs.litellm.ai/docs/proxy/cost_tracking); reconcile LiteLLM spend logs against provider Cost APIs/invoices and report the provider figure as truth.
  - Provider list-price changes mid-pilot would corrupt before/after — snapshot pricing at pilot start.
  - OpenRouter analytics lookback is only 30 completed days (https://openrouter.ai/docs/api-reference/analytics/get-activity) — export baseline before any migration, or the "before" disappears.
