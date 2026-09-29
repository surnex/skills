---
name: surnex
description: Operate Surnex, an SEO platform, through its MCP server — rank tracking, backlinks, site audits, keyword research, local SEO, and AI-search visibility (AI Overviews, ChatGPT, GEO). Use when the user asks about their search rankings, keyword positions, backlink profile, technical SEO issues, competitor comparison, or whether AI answers cite their brand; and whenever Surnex tools are available and the task concerns a tracked domain.
---

# Surnex

Surnex tracks how domains perform in search — traditional results and AI-generated answers. You reach it through 97 MCP tools at `https://api.surnex.io/mcp`.

Read this before your first tool call. Most mistakes with Surnex come from assuming it behaves like a live query API. It does not.

## The one thing to understand first

**Reading a tool does not collect data.** Almost every tool returns what a background job last wrote. Calling `get_ranking_overview` does not check rankings; it reads the last check.

Data reaches Surnex three ways, and knowing which applies stops you misreporting a stale number as a current one:

| Mode | Features | Refreshes |
| --- | --- | --- |
| **Scheduled** | Rank tracking, backlinks, GEO, local SEO | On the project's schedule — daily at 00:00 UTC by default, GEO and local SEO weekly |
| **On demand** | Site audits, web vitals, domain overview, tech stack, keyword research, AI search | **Never on their own.** Only when something starts them |
| **Live** | Trends explore, trending now | Fetched during the call |

Two consequences you will hit:

- **A new project is empty for a few minutes.** Creating one queues its first run straight away — rankings within minutes; backlinks, AI visibility, an audit and the rest as each job finishes — and that run spends some of the plan's allowance. After it, the four schedules keep the data fresh. An empty page right after creation is that run still working, not a failure. **Local SEO is the exception:** it has no first run and stays empty until someone adds a location (the business's Google listing) and keywords at it — see `references/workflows.md`.
- **An old audit score means nobody has crawled.** Audits never re-run themselves. If the user wants current technical data, call `start_site_audit`.

## Start here, every time

1. `list_organizations` — confirms who you're acting for.
2. `list_projects` — gives you the `project_id` nearly every other tool needs.

Never guess a `project_id`. A project you can't access returns "not found" rather than "not yours", so a wrong id looks identical to a deleted one.

## Naming the organization

Org-scoped tools take an optional `organization` argument (name or id).

- One organization → resolved automatically.
- Several, none named → **the call is refused** and the error lists them. This is deliberate; the server will not guess.
- Several, ambiguous name → refused, listing matches with ids.

If `list_organizations` returns more than one, pass `organization` on every org-scoped call rather than waiting to be refused.

## Spending the user's money

These tools call paid providers and draw on the user's plan allowance:

- **Keyword allowance** — `research_keyword`, `bulk_research_keywords`, `get_keyword_suggestions`, `get_domain_keywords`, `get_serp_results`, and the trends tools
- **Backlink allowance** — `refresh_backlinks`, `get_backlink_gap`
- **Domain allowance** — `get_domain_overview` and the other domain lookups
- **Monthly audit allowance** — `start_site_audit` (pages crawled; with JavaScript rendering each page costs 4)
- **AI-search allowance** — `get_google_ai_mode`, `get_chatgpt_visibility`, `benchmark_llm_platforms`, `get_ai_keyword_trends`, `get_citation_gap`
- **Content allowance** — `generate_meta_tags`, `generate_content_brief`, `check_grammar`, `paraphrase_text`

Rules that follow:

- **Never loop a billable tool over a list** without the user asking for that scale. Fifty keywords through `research_keyword` one at a time is fifty charges; `bulk_research_keywords` is one call.
- **Bound the scope and say what it costs.** "I'll benchmark your top 10 commercial queries" beats silently doing 200.
- Call `get_usage_summary` first when a request implies volume. Hitting a limit mid-run leaves a partial result.

Reading stored data is free. Prefer it.

## Destructive and one-at-a-time

`delete_project` is annotated destructive. It removes the project, all its ranking and backlink history, audits, reports, and share links, and **none of it can be recovered** — history is accumulated daily and cannot be back-filled. Confirm with the user in plain terms before calling it, every time.

Other writes — `remove_tracked_keywords`, `remove_competitor`, `remove_geo_topics`, `remove_local_location`, `delete_saved_keywords` — also destroy history. Removing a tracked keyword deletes its position history; **pausing** it via `update_tracked_keyword` keeps the history. `remove_local_location` takes every keyword at that location and their grids with it.

A tracked keyword can't be edited — `update_tracked_keyword` only pauses or resumes. To move one to another market, add it again with the new location, language or engine; each market is its own keyword with its own history.

`start_site_audit` and `trigger_web_vitals_check` refuse to start if one is already running for that project. That's a refusal, not a queue. Don't retry in a loop; wait.

## Jobs are asynchronous

Tools that start work return a job with `status: "pending"`. Poll for a terminal status — `completed` or `failed`.

Match the interval to the work: rank checks and backlink analyses in minutes, a site audit anywhere from minutes to an hour. Don't poll a crawl every second; you'll burn the request rate limit (120/min for a key, 60/min for a user) without arriving sooner.

A failed job returns a **successful** response containing `status: "failed"` and a message. Check the job status, not just whether the call succeeded.

## You act as the user

The MCP connection uses OAuth. The token identifies a person, carries **their role**, and reaches every organization they belong to.

- A **member** cannot create or delete projects. Both need admin.
- Only an **owner** can change billing.
- No session can mint an API key, change billing, invite or remove people, or reach an organization the user isn't a member of.

If a tool is refused, check the role before assuming a bug.

## Locale is not inherited

Tools taking a location and language default to **2840** (United States) and **en**. They do **not** pick up the project's configured market.

If the user's project targets anywhere else, pass the codes explicitly on every call. Silently returning US data for a UK site is the most common way to be confidently wrong here.

## Empty is usually not broken

Three different causes, and they need different answers:

| Cause | Tell the user |
| --- | --- |
| No scheduled run yet | When the schedule next runs |
| It's an on-demand feature | Offer to start it |
| Collected and found nothing | It succeeded — a new domain genuinely has no backlinks |

Check which before reporting a problem. "No backlinks found" on a three-week-old site is a true finding, not a fault.

## Reference

Load these when the task calls for them:

- **`references/tools.md`** — all 97 tools by area, with which write and which are billable
- **`references/workflows.md`** — worked recipes: weekly review, audit triage, link-gap prospecting, AI-visibility audit, local SEO setup, monthly reporting
- **`references/interpreting.md`** — what the numbers mean and how they mislead: alert thresholds, audit scoring, keyword metrics, AI-answer variance
- **`references/troubleshooting.md`** — auth failures, refusals, and empty results

## Working well

**Answer the question, don't dump the tool output.** These tools return large objects. The user asked what changed, not for a table of 500 rows. Chain the calls, do the comparison, and report the conclusion.

**Cross-reference — that's the advantage over the dashboard.** "Which keywords lost top-10 positions, and do those pages have audit issues?" is not a screen in the product. It's two tool calls and a join, and it's the kind of thing worth doing unprompted when it sharpens the answer.

**Say when data is stale.** If the last rank check was six days ago because the project is on a weekly cadence, say so alongside the number.

**Prefer one broad call to many narrow ones** — `bulk_research_keywords` over repeated `research_keyword`, one large audit over several small ones. It's cheaper for the user and faster for you.
