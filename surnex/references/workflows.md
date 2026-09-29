# Surnex workflows

Worked recipes. Each names the tools, the order, and the judgment the tools can't supply.

## Weekly review

**Goal:** what changed, and what needs attention.

1. `get_ranking_changes` over the last 7 days
2. `get_new_lost_backlinks`
3. `get_audit_summary` on the most recent audit
4. `get_alerts` with unread only

All free — stored data. Then:

- Lead with **losses**, not gains. A keyword that left the top 10 costs more than one that moved 40 → 32.
- Check the audit's date. If it's weeks old, say so rather than reporting it as current.
- Net the backlinks: 40 gained and 38 lost is flat, however good the gained list looks.

Don't list every change. Pick the handful that matter and say why.

## Audit triage for developers

**Goal:** a prioritized fix list someone can action.

1. `list_audits` → newest
2. `get_audit_summary`
3. `get_audit_issues`, filtered `severity: "error"` first

Then order by **work per issue resolved**, not by issue count:

1. **Sitewide patterns.** An issue on every page is usually one template change. Fixing a missing meta description in a layout can clear hundreds.
2. **Errors.** −2 each: missing title, missing description, missing H1, broken internal links, redirect loops, broken images, mixed content, missing HTTPS.
3. **The three bonuses.** HTTPS, `robots.txt`, sitemap — +5 each, 15 points for an afternoon.
4. **Warnings**, largest groups first.
5. **Notices** — usually intentional. A `noindex` on a thank-you page is correct.

Use `get_audit_page_detail` when an issue doesn't make sense. Three fields explain most confusion: **Word Count** (thin content that's actually client-rendered), **Canonical URL** (pointing elsewhere, so the page is deliberately de-indexed), **Render-Blocking Scripts** (the real cause of a slow load time).

If the crawl's page limit was low, say so — duplicate titles, orphan pages, and thin content are all under-reported by a narrow crawl.

## Link-gap prospecting

**Goal:** ten domains worth approaching.

1. `get_competitors`, or ask which domains to compare
2. `get_backlink_gap` with **up to 4** competitor domains

Rank the results:

- **Domains linking to all four** — directories, roundups, category resources. Strongest targets: your absence is conspicuous rather than a matter of taste.
- **Then by authority.** High rank plus linking-to-everyone is the best prospect on the page.
- **Linking to one competitor** — likely a relationship or paid placement. Winnable, but not by asking.
- **High link count to one competitor** — sitewide arrangement, not an editorial mention. Deprioritize.

Compare four at once, not one. With one competitor you get a list of their links and no way to tell systematic coverage from a one-off.

## AI-visibility audit

**Goal:** where AI answers are displacing the user, and what to do.

1. `get_ai_overview_summary` — read the **trigger rate** first. Near zero means AI search isn't affecting this market yet; say so and stop. Don't spend the AI-search allowance proving a negative.
2. `get_ai_overview_keywords` — find rows where the user **ranks well but isn't cited**. Highest-value gap: the authority exists, the answer is being given without them.
3. `get_citation_gap` on the top commercial queries with their competitors
4. `get_chatgpt_visibility` on two or three confirmed gaps — the **snippet** field shows the exact passage that earned a competitor the citation

Then split the finding in two, because the fixes are unrelated:

- **Their own page isn't quotable.** Answer the question directly and early, match how people phrase it, state facts so they survive being quoted in isolation.
- **Third-party sources own the topic.** Frequently publications, docs, forums, comparison sites. No amount of on-site work helps; the work is getting covered there. If a comparison site is cited and doesn't list them, that's the whole finding.

**Always caveat variance.** AI responses aren't deterministic. One absence is a sample, not evidence — re-run anything commercially important before concluding.

## Keyword expansion

**Goal:** find terms worth tracking.

1. `get_domain_keywords` on the user's domain — what they already rank for
2. `get_domain_keywords` on 2–3 competitors — what they're targeting
3. `bulk_research_keywords` on the shortlist (one call, not one per keyword)

Judge on three questions, in this order:

- **Is the intent right?** A transactional keyword needs a product page. Format mismatch doesn't rank however good the writing.
- **Is it winnable?** Difficulty is relative to the whole web, not to this domain. Judge it against their `get_domain_overview` authority.
- **Is it worth anything?** CPC is the best proxy for commercial value. High volume with near-zero CPC is traffic that rarely converts.

The sweet spot on most sites is low volume, low difficulty, high CPC. Positions **11–20** with real volume are the cheapest wins — already ranking, one page short.

Then `save_keywords` for the shortlist, `add_tracked_keywords` for what graduates. Remember tracking is a recurring daily cost; saving is free.

## Monthly client report

**Goal:** a report that gets sent.

Surnex reports are built in the dashboard, not through MCP. What you can do is assemble the substance:

1. `get_ranking_overview` and `get_ranking_changes` for the period
2. `get_project_backlink_history`
3. `compare_audits` between the period's two crawls
4. `get_ai_overview_summary` if AI visibility is in scope

Then write the narrative — which is the part that's actually hard. Include what got worse; a report with only good news stops being believed. Explain the causes you can see and name the ones you can't.

Tell the user the report itself is built under **Reports** in the dashboard, and that only a PDF export is a frozen snapshot — a share link keeps showing live data.

## Setting up a new project

1. `create_project` with its keywords — needs **admin**. It queues the first run at once: rank check, backlinks, AI visibility, local, a site audit, web vitals and domain data. That run draws on the allowance (a few research lookups, one AI brand audit, up to 100 audit pages).
2. **Set the schedules.** Three default to daily at 00:00 UTC; AI visibility defaults to weekly. Move the run hour to suit when the user reads their data.
3. Lower what doesn't need daily — backlinks especially. Link profiles barely move day to day and each snapshot spends allowance.
4. `add_competitor` for who to compare against, and `add_tracked_keywords` for keywords beyond the first set.
5. Later audits: `start_site_audit` — only the first one runs by itself.

Set expectations explicitly: rankings arrive within minutes, everything else as each job finishes.
