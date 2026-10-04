# Interpreting Surnex data

What the numbers mean, and the specific ways they mislead.

## Ranking alerts

Alerts fire on a rank check by comparing to the previous one. The rules are fixed:

| Condition | Alert |
| --- | --- |
| Wasn't ranking, now is | Ranking Improved (change 0) |
| Was ranking, now isn't | Lost Top 100 (change 0) |
| Moved **5 or more** places | Ranking Improved / Ranking Dropped |
| Crossed into 1–3 | Entered Top 3 |
| Crossed into 1–10 | Entered Top 10 |
| Fell out of the top 10 | Lost Top 10 |

The first two are terminal — nothing else fires alongside them. The rest combine.

Consequences worth knowing before you explain a missing alert:

- **A 4-place move fires nothing.** Positions fluctuate constantly; alerting on that would make the feed useless.
- **One check can fire three alerts.** 14 → 2 is Ranking Improved, Entered Top 10, and Entered Top 3 — one event, three entries. Don't report it as three things happening.
- **12 → 9 fires only Entered Top 10.** A small move crossing a band matters more than a large one that doesn't.
- **There is no Lost Top 3.** Falling 2 → 7 raises Ranking Dropped only.
- **The first check ever fires nothing** — no previous position to compare.

"Not ranking" means outside the top 100, not absent from Google.

## Audit score

Starts at 100:

- −2 per error, capped at 50 total
- −0.5 per warning, capped at 30
- −0.1 per notice, capped at 10
- +5 each for HTTPS, `robots.txt`, a sitemap
- Clamped to 0–100

Two things follow. Because deductions cap, **a site with 25 errors scores the same as one with 250** — past the cap the score stops discriminating. And the bonuses are flat, so a small site missing a sitemap is penalized as hard as a large one.

Report the score for trend, work from the issue list for action.

Thresholds behind specific issues: slow page **3,000 ms**, large page **3,072 KB**, thin content **300 words**, titles **50–60 chars**, descriptions **120–155 chars**.

## Keyword metrics

**Volume** is a 12-month average in the project's market. A strongly seasonal term shows a figure it rarely actually hits — check the trend before treating it as a monthly forecast.

**Difficulty** is 0–100, colour-banded green below 40, amber to 69, red above. It's relative to the whole web, **not** to this domain. A difficulty of 35 is easy for an established site and out of reach for a new one. Always judge it against `get_domain_overview` authority.

**CPC** is the best available proxy for commercial value. Modest volume with high CPC converts; high volume with near-zero CPC is informational traffic.

**Competition** is a *paid search* metric. It says nothing about organic difficulty. A keyword can be LOW competition with a difficulty of 80. Never substitute one for the other.

**Intent** decides what to build, and it's the metric most often ignored. A transactional keyword targeted with a blog post loses to a product page regardless of quality.

Metrics are captured when the lookup runs and never refresh themselves. A saved keyword shows the figures from the day it was saved.

## Rankings

**Average position counts a keyword outside the top 100 as 100** — Semrush's rule. Every checked keyword is in it; one not yet checked is not. So losing a keyword makes the average worse and gaining one makes it better, and a project tracking many keywords it doesn't rank for reads high: 2, 6 and not ranking average 36, not 4. Before calling a high average bad, look at how much of it is `not_ranking` in the distribution — those are targets, not failures of the pages that do rank.

**Movement is four counts, as Semrush shows it.** `get_ranking_overview` compares each keyword's latest check with the one before:

| Field | Means |
| --- | --- |
| `improved` / `declined` | In the top 100 both times, moved up / down |
| `entered_top100` | Not in the top 100 last check, is now |
| `lost_top100` | In the top 100 last check, isn't now |

A keyword's first check is in none of them. A tracked keyword's `checked_before` says which case a null `previous_position` is: `false` is a first check, `true` is "outside the top 100 last time". Alerts count the same moves differently — entering the top 100 raises Ranking Improved, not a separate alert — so the alert feed and these counts won't match one for one.

**Position bands are exclusive.** 1–3, 4–10, 11–20, 21–50, 51–100, not ranking. They sum to the total tracked. "In top 10" is the first two bands combined.

**The ranking URL matters.** When it changes between checks, Google reinterpreted which page is most relevant — often the real story behind a swing that looks inexplicable from the number.

## Backlinks

Read the three headline figures together:

- **Total backlinks ≫ referring domains** — a few sites linking many times. A sitewide footer link, not thousands of endorsements.
- **Referring domains ≫ referring IPs** — many "different" sites on shared hosting, often one operator.
- Healthy profiles grow all three together.

**Semantic location** is the quality signal most people skip: a body-content link is an editorial endorsement, a footer or sidebar link is boilerplate.

**Anchor text** matters in aggregate. Naturally-earned profiles are mostly branded and generic. Exact-match commercial anchors dominating is the classic signature of bought links.

**Broken backlinks** are the actionable row — authority already earned, currently going nowhere. Redirect the dead URL and it's recovered without earning anything new.

A cliff in the trend chart is more often a large site dropping out of the index than a mass unlinking.

## AI visibility

**Trigger rate** — the share of tracked keywords producing an AI answer. Not controllable; it's exposure.

**Citation rate** — of those, how many cite the domain. This is the movable number, and it's a percentage **of triggered keywords, not of all keywords**. 50% citation on a 4% trigger rate means cited in half of very few.

Both describe the **newest rank check** (`as_of` in `get_ai_overview_summary`), not every overview ever seen. For movement over time use `get_ai_overview_trend`.

**Mentions and citations are different.** Mentioned without cited means brand awareness without being treated as the source — a content problem.

**Responses are non-deterministic.** The same query returns different sources on different runs. A single absence is a sample. State this whenever you report an AI-visibility finding; it's the most common way to over-conclude here.

**Ranking doesn't transfer.** Position one grants nothing in ChatGPT. Sites that rank modestly are cited regularly, and the reverse.

## Web vitals

Raw Lighthouse values, no pass/fail grade. Google's own "good" thresholds for reference: LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1.

The supporting metrics explain the Core ones:

- **High TTFB with high LCP** — server-side. Front-end work won't fix a slow origin.
- **Low TTFB, high LCP** — heavy page. Images and render-blocking resources.
- **High TBT with poor INP** — JavaScript monopolizing the main thread.

These are **lab** measurements from one synthetic load. They won't match Search Console field data, which is typically worse. Good for before-and-after comparison, not for predicting what Google sees.

## Trends

Values are **relative and indexed to the series peak**, not search volumes. Two terms on one chart are comparable to each other; a value of 100 is that term's own maximum. For absolute volumes use the keyword tools.

## Local SEO

Local rankings are unrelated to organic ones for the same term. Position 3 vs 4 matters enormously — the Maps pack typically shows three before requiring a click.

Proximity dominates, which is why each keyword is searched from every point of a grid around the listing. Read a grid as a map, not an average:

- **`top3_share`** (0–1) — the share of grid points where the business is in the top 3. The headline figure, and the one to trend.
- **`average_position`** — only over points where it was found. It can improve because the business dropped out of the points where it ranked worst; always read it next to `found` / `cells`.
- **`position: null`** — not in the top 20 at that point. Paid Maps results are excluded from positions.
- **Strong centre, weak edges** is the normal shape. The centre cell is what `get_local_rankings` and the overview's "in the pack" count report — a single-point view that overstates reach.

The business profile — categories, reviews, hours — matters more than the website. Local results are more volatile than organic; compare several checks before concluding anything.
