# Troubleshooting Surnex

## "This endpoint uses OAuth, not API keys"

The user configured an API key. The MCP server refuses them — it uses OAuth, and the client obtains a token itself.

Their config should contain the URL and nothing else:

```json
{
  "mcpServers": {
    "surnex": {
      "type": "http",
      "url": "https://api.surnex.io/mcp"
    }
  }
}
```

Note for the user: the **Settings → API Keys** page in the Surnex dashboard still displays MCP snippets with an `X-API-Key` header. Those snippets are stale and produce exactly this error. Only the `curl` example on that screen is correct, and it's for the REST API.

## "You belong to more than one organization, so this call is ambiguous"

Not a failure — the server refusing to guess. The message lists the organizations with their ids.

Retry with `organization` set. If you're going to make several calls, set it on all of them rather than being refused each time.

## Access drops roughly hourly

The token was issued without `offline_access`, so there's no refresh token. Tell the user to reconnect and confirm the consent screen shows *"Stay connected without asking you again every hour"*.

## A tool is refused but the data exists

The token acts as the user, carrying their role.

- **Member** — cannot create or delete projects
- **Admin** — cannot change billing
- **Nobody** — can mint an API key, change billing, invite or remove people, or reach an organization they're not a member of

Check the role before reporting a bug.

## Empty results

Establish which of three causes applies before calling it a problem:

**First run still going, or no scheduled run yet.** A new project's first run starts at creation and fills pages as each job finishes. After that, rank tracking and backlinks run daily at 00:00 UTC by default, GEO and local SEO weekly. Report when the next run is due.

**Local SEO not set up.** It has no first run. Until a location is added (`search_local_listings` → `add_local_location`) and keywords are added at it, every local tool returns nothing. Offer to set it up. Adding keywords queues the first check at once.

**On-demand feature never started.** Audits, web vitals, domain overview, tech stack, keyword research, and AI search have **no schedule**. They only run when something starts them. Offer to.

**Genuinely nothing there.** A new domain has no backlinks. A keyword searched where the business isn't in the top 20 shows `null` at every grid point. A market with no AI Overviews returns no AI data. These are findings, not faults.

**An audit failed with "could not read any pages".** The crawler was blocked, hit a login, or got no answer. That's not a clean site — check `robots.txt`, firewalls and the target URL, then re-run.

## Ranking changes shows nothing

Movement needs two checks to compare. A project on its first day has none, and one on a weekly cadence produces roughly four data points a month — a one-day comparison window will usually be empty.

Widen the range, or check the schedule cadence before concluding rankings are static.

## Audit results look wrong after a fix

Two likely causes:

**The score didn't recalculate.** It belongs to the crawl that produced it. A new crawl is needed; nothing recomputes.

**The crawls aren't comparable.** `compare_audits` between a 50-page and a 500-page crawl reports hundreds of "new" issues that were always there and simply weren't crawled. Same target URL, same page limit, or the comparison is meaningless.

## A second audit won't start

Only one crawl per project at a time. A request while one is in flight is **refused, not queued**. The same applies to web vitals checks. Wait for the running job rather than retrying in a loop.

## Usage limit errors

The plan allowance is exhausted, not the request rate. Waiting a minute changes nothing — daily counters reset the next day, the monthly audit allowance at the start of the period.

Call `get_usage_summary` to see what's left. Note that the four collection schedules all default to daily, which consumes allowance continuously; lowering the ones the user doesn't watch closely is usually the fix, ahead of upgrading.

## Rate limit errors

Different problem: too many requests per minute — 120 for an API key, 60 for a user. Usually caused by polling a job too aggressively or paginating in a tight loop.

Back off, raise page sizes, and poll long-running jobs on an interval matched to the work.

## Results are for the wrong country

Research, trends and domain lookups default to **2840** (United States) and **en** — they do **not** inherit the project's market. Pass the codes explicitly. Project tools (adding keywords and prompts, the AI searches, the listing search) start on the project's own market for that service when the codes are left out.

This fails silently — you get plausible US data for a UK site — so check it whenever a figure looks unexpectedly off.

## A project "disappeared"

A project the caller can't access returns **not found**, never "not yours". So a not-found is ambiguous between deleted, wrong id, and wrong organization.

Call `list_projects` before concluding anything was deleted. If the user belongs to several organizations, check the others.
