# Surnex MCP tools

95 tools. Most take a `project_id` from `list_projects`. Org-scoped tools take an optional `organization` (name or id) — required in effect when the user belongs to more than one.

Legend: **W** writes · **$** spends plan allowance · **X** destructive and irreversible

## Onboarding — start here

| Tool | | Notes |
| --- | --- | --- |
| `list_organizations` | | First call of any session |
| `list_projects` | | Source of every `project_id` |
| `create_project` | W | Requires admin. Takes name, domain and keywords; queues the first run at once, which spends some allowance. A market per service: tracking (`location_code`, `language_code`, `search_engine`), research (`research_*`, a pair from `list_search_languages` for=research) and AI visibility (`ai_*`, a country and a for=ai_visibility language). One left out is the tracking market if that service takes it, else refused naming the fields |
| `delete_project` | X | Irreversible, cascades to all history. Confirm explicitly |
| `get_usage_summary` | | Call before anything with volume |

## Rank tracking — 21

| Tool | | Notes |
| --- | --- | --- |
| `get_ranking_overview` | | Totals, average position (outside the top 100 counts as 100), distribution, improved / declined / entered / lost top 100 |
| `get_tracked_keywords` | | |
| `get_keyword_position` | | One keyword's current standing |
| `get_keyword_ranking_history` | | Positions over a date range |
| `get_ranking_changes` | | Movement between two dates |
| `get_competitor_comparison` | | Your position vs competitors, per keyword. Paged: `page`, `per_page` (25), `search` |
| `find_location` | | Free. A place's `location_code` by name — `austin`, `austin, texas`, `10001` — for an `engine`; countries, regions, cities, postal codes. Optional `country` (ISO) narrows it |
| `list_search_languages` | | Free. `for`: tracking (default) — an engine's `language_code`s, any with any of its locations; ai_visibility — languages all six AI engines answer in; research — research's country-and-language pairs. Baidu: `zh-CN` only; Seznam: `cs` only; Naver: none. Search volume exists only in Google Ads' 46 languages (`pt`, not `pt-BR`); a keyword in another tracks with no volume |
| `add_tracked_keywords` | W | `search_engine`: google, bing, yahoo, baidu, naver, seznam. Any place and language the engine searches from (a 400 names what it refuses); Naver takes neither and is stored as Korea/Korean. Counts against the plan's keyword limit. The same keyword in another location, language or engine is a separate keyword. Added keywords get a first check at once — positions in about 20 minutes. Re-adding a paused one leaves it paused |
| `update_tracked_keyword` | W | Pause or resume only (`is_active`) — keeps history. There is no edit; to change market, add it again |
| `remove_tracked_keywords` | X | Deletes the keyword's entire position history |
| `list_tags` / `create_tag` / `assign_tags` | W | Grouping the dashboard barely surfaces |
| `get_alerts` | | Threshold-crossing events |
| `mark_alert_read` / `mark_all_alerts_read` | W | |
| `get_ai_overview_summary` | | Trigger rate and citation rate on the **newest check** (`as_of`) |
| `get_ai_overview_trend` | | Those rates over time |
| `get_ai_overview_keywords` | | Per-keyword AI Overview detail |
| `export_tracking_csv` | | Bulk read without paginating |

## Keywords — 10

| Tool | | Notes |
| --- | --- | --- |
| `research_keyword` | $ | One keyword. Prefer bulk for lists |
| `bulk_research_keywords` | $ | Up to 700 in one call |
| `get_keyword_suggestions` | $ | Up to 100 seeds → related terms |
| `get_domain_keywords` | $ | Works on **any** domain, including competitors |
| `get_serp_results` | $ | Live results for a query |
| `get_keyword_history` | | Stored, free |
| `get_saved_keywords` | | The user's shortlist |
| `save_keywords` | W | Bookmark only — does **not** start tracking |
| `delete_saved_keywords` | W | Removes the bookmark, not any tracking |
| `export_keywords_csv` | | |

Saved and tracked are separate systems with no promotion between them. To track a saved keyword, call `add_tracked_keywords` with its text.

## Backlinks — 11

| Tool | | Notes |
| --- | --- | --- |
| `get_backlink_summary` | | Domain rank, totals, dofollow split, spam score |
| `get_referring_domains` | | One row per linking site. Paged |
| `get_backlinks_list` | | One row per link. Paged; `dofollow_only` |
| `get_anchor_texts` | | Anchor distribution — each anchor's share is of all anchors, not the page. Paged |
| `get_new_lost_backlinks` | | Last 30 days. Paged, with both counts |
| `get_tld_distribution` | | |
| `get_project_backlink_history` | | The growth trend |
| `get_backlink_gap` | $ | Your project's site against ad-hoc competitor domains, **up to 4 per run**. Waits up to two minutes; answers with the first 25 referring domains and the total |
| `get_backlink_gap_result` | | A gap already started — by id, or the latest. Paged (`page`, `per_page`, `search`). Free |
| `refresh_backlinks` | W $ | Starts a job |
| `export_backlinks_csv` | | |

The paged tools take `page`, `per_page` (default 25, up to 100) and `search`, and return `total`. Read the first page and ask for more only when the question needs it. `export_backlinks_csv` has every individual backlink (that list only), and `export_audit_csv` every issue.

`get_backlink_gap` does not read the project's competitor list — pass domains explicitly. If it answers `still_running`, collect with `get_backlink_gap_result` and the `gap_id`: calling `get_backlink_gap` again buys a second analysis.

## Audits — 10

| Tool | | Notes |
| --- | --- | --- |
| `start_site_audit` | W $ | The project's own site or a subdomain only; defaults to its home page. One crawl at a time per project. Set the page limit deliberately. JavaScript rendering costs 4 pages per page crawled |
| `list_audits` | | |
| `get_audit_summary` | | Score, pages crawled, issue counts |
| `get_audit_issues` | | Filterable by severity and category |
| `get_audit_pages` | | |
| `get_audit_page_detail` | | The measurements behind an issue |
| `get_duplicate_content` | | Paged by group |
| `get_broken_resources` | | Paged by resource |
| `compare_audits` | | Two crawls — resolved vs new issues |
| `export_audit_csv` | | |

`compare_audits` is only meaningful between crawls of the **same page limit and target URL**.

## AI search — 5 (all billable)

| Tool | | Notes |
| --- | --- | --- |
| `get_google_ai_mode` | $ | One query |
| `get_chatgpt_visibility` | $ | Response plus cited sources with snippets |
| `benchmark_llm_platforms` | $ | Several platforms at once — costs more |
| `get_ai_keyword_trends` | $ | AI-search volume and velocity |
| `get_citation_gap` | $ | Up to 10 competitors × 50 keywords |

## GEO — 7

| Tool | | Notes |
| --- | --- | --- |
| `get_ai_visibility_overview` | | The GEO summary — average mentions and citation rate, and the daily trend over `days` (default 30). Start here |
| `get_ai_top_competitors` | | Who owns a prompt in AI answers |
| `get_llm_response_for_keyword` | W $ | Queues a fresh snapshot of one prompt across all six engines — one research lookup. Read the answer with `get_geo_topic_detail` |
| `list_geo_topics` | | |
| `get_geo_topic_detail` | | Daily mentions and citation rate; sources and answers for the **newest** snapshot only |
| `add_geo_topics` | W | One prompt per location and language. Added prompts get a first snapshot at once (covered by the plan); then weekly by default — each prompt is a recurring check |
| `remove_geo_topics` | X | Deletes the prompt's snapshot history |

## Domains and tech stack — 5

| Tool | | Notes |
| --- | --- | --- |
| `get_domain_overview` | $ | Authority, traffic estimate, keyword count |
| `get_domain_top_keywords` | $ | The domain's highest-traffic keywords, with position, URL and traffic — a shortlist source |
| `get_domain_top_pages` | $ | Traffic, keyword count and traffic value per page. No per-page backlink counts |
| `get_domain_competitors` | $ | **Discovered** rivals, not the configured list: whole-site keywords and traffic, common keywords, avg position. No rank |
| `get_tech_stack` | $ | Categories grouped by type. No versions or confidence |

Traffic figures are modelled estimates, not analytics. Use them comparatively. Domain arguments accept a URL and reduce it to the bare domain. These are billed to the organization: they take an optional `organization`, not a `project_id`, and work while the only project is the sample.

## Competitors — 3

| Tool | | Notes |
| --- | --- | --- |
| `get_competitors` | | The project's configured list |
| `add_competitor` | W | Feeds tracking comparison, GEO, citation gap |
| `remove_competitor` | W | |

## Local SEO — 13

| Tool | | Notes |
| --- | --- | --- |
| `get_local_overview` | | In the pack at each location's centre, plus grid top-3 share and average position |
| `list_local_locations` | | Each location's listing, grid (`grid_size`, `spacing_km`) and keyword count |
| `search_local_listings` | | Find a Google Business listing by name and town. **Not charged**, but a live search — search once |
| `add_local_location` | W | A listing plus its grid: `grid_size` 3, 5 or 7, `spacing_km` apart. Uses one of the plan's local locations |
| `update_local_location` | W | Rename, re-draw the grid (`spacing_km` 0.5, 1, 2 or 5), or pick the listing again. Keeps keywords and history |
| `remove_local_location` | X | Deletes its keywords and their history; frees the location |
| `get_local_keywords` | | Each keyword at its location (`place_id`), with its newest grid summary |
| `add_local_keywords` | W | `location_id` + up to 10 keywords per location. Queues a first check of the added keywords at once |
| `update_local_keyword` | W | `is_active` false pauses, true resumes. Keeps history; still takes a place |
| `remove_local_keywords` | X | Deletes keywords and their grid history; frees room at the location |
| `get_local_grid` | | One keyword's position at every grid point, the top 3 at each, and every check's summary. Optional `date` |
| `get_local_rankings` | | The Maps pack at each location's centre, with ratings and reviews. Latest check by default (`latest_only`), `own_only` for your listing |
| `get_google_business_profile` | | |

Order: `search_local_listings` → `add_local_location` → `add_local_keywords` → `get_local_grid`. A location is the business's own listing, matched by its Google `cid`, so a business without a website works. Keywords add no location; a location holds at most 10. A grid point's `position: null` means not in the top 20; paid Maps results are excluded. A location made before listings existed has no coordinates and is searched once for its market until a listing is picked (in the dashboard).

## Web vitals — 2

| Tool | | Notes |
| --- | --- | --- |
| `trigger_web_vitals_check` | W $ | Per URL **and per device** (mobile or desktop). One at a time per project. A URL without `https://` is accepted |
| `get_web_vitals` | | Raw metric values, no pass/fail grade |

Each check uses one page of the monthly site audit allowance.

## Trends — 3 (all billable, all live)

| Tool | | Notes |
| --- | --- | --- |
| `get_trends_explore` | $ | Up to 5 terms. Values are **relative**, not volumes |
| `get_trending_now` | $ | |
| `get_related_queries` | $ | |

There are no content-writing tools. To draft a content brief, meta tags or copy, write it yourself from the research tools' output — `research_keyword`, `get_keyword_suggestions`, `get_ai_top_competitors`.

## Free vs billable, at a glance

Everything under rank tracking, GEO reads, audit reads, backlink reads, and competitor reads is stored data — free and fast. Every tool that reaches an external provider costs money.

When a request can be answered from stored data, answer it from stored data.
