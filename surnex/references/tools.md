# Surnex MCP tools

88 tools. Most take a `project_id` from `list_projects`. Org-scoped tools take an optional `organization` (name or id) — required in effect when the user belongs to more than one.

Legend: **W** writes · **$** spends plan allowance · **X** destructive and irreversible

## Onboarding — start here

| Tool | | Notes |
| --- | --- | --- |
| `list_organizations` | | First call of any session |
| `list_projects` | | Source of every `project_id` |
| `create_project` | W | Requires admin. Takes name and domain; collects nothing immediately |
| `delete_project` | X | Irreversible, cascades to all history. Confirm explicitly |
| `get_usage_summary` | | Call before anything with volume |

## Rank tracking — 19

| Tool | | Notes |
| --- | --- | --- |
| `get_ranking_overview` | | Totals, average position, distribution |
| `get_tracked_keywords` | | |
| `get_keyword_position` | | One keyword's current standing |
| `get_keyword_ranking_history` | | Positions over a date range |
| `get_ranking_changes` | | Movement between two dates |
| `get_competitor_comparison` | | Your position vs competitors, per keyword |
| `add_tracked_keywords` | W | Counts against the plan's keyword limit |
| `update_tracked_keyword` | W | Use with `is_active: false` to **pause** — keeps history |
| `remove_tracked_keywords` | X | Deletes the keyword's entire position history |
| `list_tags` / `create_tag` / `assign_tags` | W | Grouping the dashboard barely surfaces |
| `get_alerts` | | Threshold-crossing events |
| `mark_alert_read` / `mark_all_alerts_read` | W | |
| `get_ai_overview_summary` | | Trigger rate and citation rate |
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

## Backlinks — 10

| Tool | | Notes |
| --- | --- | --- |
| `get_backlink_summary` | | Domain rank, totals, dofollow split, spam score |
| `get_referring_domains` | | One row per linking site |
| `get_backlinks_list` | | One row per link |
| `get_anchor_texts` | | Anchor distribution |
| `get_new_lost_backlinks` | | Last 30 days |
| `get_tld_distribution` | | |
| `get_project_backlink_history` | | The growth trend |
| `get_backlink_gap` | $ | Ad-hoc competitor domains, **up to 4 per run** |
| `refresh_backlinks` | W $ | Starts a job |
| `export_backlinks_csv` | | |

`get_backlink_gap` does not read the project's competitor list — pass domains explicitly.

## Audits — 10

| Tool | | Notes |
| --- | --- | --- |
| `start_site_audit` | W $ | One crawl at a time per project. Set the page limit deliberately |
| `list_audits` | | |
| `get_audit_summary` | | Score, pages crawled, issue counts |
| `get_audit_issues` | | Filterable by severity and category |
| `get_audit_pages` | | |
| `get_audit_page_detail` | | The measurements behind an issue |
| `get_duplicate_content` | | |
| `get_broken_resources` | | |
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
| `get_ai_visibility_overview` | | Topic-level share of voice |
| `get_ai_top_competitors` | | Who owns a topic in AI answers |
| `get_llm_response_for_keyword` | | The raw response |
| `list_geo_topics` | | |
| `get_geo_topic_detail` | | Snapshot history and cited sources |
| `add_geo_topics` | W | Scheduled daily — each topic is a recurring cost |
| `remove_geo_topics` | X | Deletes the topic's snapshot history |

## Domains and tech stack — 5

| Tool | | Notes |
| --- | --- | --- |
| `get_domain_overview` | $ | Authority, traffic estimate, keyword count |
| `get_domain_top_keywords` | $ | What a domain ranks for — a shortlist source |
| `get_domain_top_pages` | $ | |
| `get_domain_competitors` | $ | **Discovered** rivals, not the configured list |
| `get_tech_stack` | | Project domain only |

Traffic figures are modelled estimates, not analytics. Use them comparatively.

## Competitors — 3

| Tool | | Notes |
| --- | --- | --- |
| `get_competitors` | | The project's configured list |
| `add_competitor` | W | Feeds tracking comparison, GEO, citation gap |
| `remove_competitor` | W | |

## Local SEO — 5

| Tool | | Notes |
| --- | --- | --- |
| `get_local_overview` | | |
| `get_local_keywords` | | |
| `get_local_rankings` | | Local pack positions |
| `add_local_keywords` | W | Separate from organic tracked keywords |
| `get_google_business_profile` | | |

Local keywords need local intent — "near me", or a place name. A bare service term returns no local pack and will never rank here.

## Web vitals — 2

| Tool | | Notes |
| --- | --- | --- |
| `trigger_web_vitals_check` | W | Per URL **and per device**. One at a time per project |
| `get_web_vitals` | | Raw metric values, no pass/fail grade |

Not metered against the plan, unlike audits.

## Trends — 3 (all billable, all live)

| Tool | | Notes |
| --- | --- | --- |
| `get_trends_explore` | $ | Up to 5 terms. Values are **relative**, not volumes |
| `get_trending_now` | $ | |
| `get_related_queries` | $ | |

## Content — 4 (all billable)

| Tool | | Notes |
| --- | --- | --- |
| `generate_meta_tags` | $ | **No dashboard equivalent** |
| `generate_content_brief` | $ | **No dashboard equivalent** |
| `check_grammar` | $ | |
| `paraphrase_text` | $ | |

The first two exist only through MCP, which makes content planning a genuinely agent-native workflow.

## Free vs billable, at a glance

Everything under rank tracking, GEO reads, audit reads, backlink reads, and competitor reads is stored data — free and fast. Every tool that reaches an external provider costs money.

When a request can be answered from stored data, answer it from stored data.
