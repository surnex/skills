# Surnex Skills

Agent skills for [Surnex](https://surnex.io), an SEO platform for rank tracking, backlinks, site audits, and AI-search visibility.

A skill gives an AI agent a workflow guide for operating Surnex, rather than leaving it to infer how the pieces fit together from tool descriptions alone. A description says what one tool does; it can't say what order to do things in, what costs money, or which empty result is a finding rather than a fault.

## Install

```bash
npx skills add surnex/skills --skill surnex
```

Restart your AI tool afterwards if it doesn't pick the skill up immediately.

Pair it with the [MCP server](https://docs.surnex.io/mcp/connect) — the skill explains the workflows, the connector executes them.

## Skills

| Skill | Covers |
| --- | --- |
| [`surnex`](./surnex) | Operating Surnex through its MCP server |

## What the `surnex` skill covers

- **The data model that trips agents up** — most tools read stored data written by background jobs. Reading rankings doesn't check rankings, a new project is empty until its first run finishes, and audits never re-run themselves.
- **Which calls cost money** — the tools that reach paid providers and draw on the user's plan allowance, and why looping one over a list is the failure mode that matters.
- **Choosing an organization** — the server refuses to guess when a user belongs to several, rather than billing the wrong one.
- **Acting as the user** — the OAuth token carries their role, so a member can't create or delete projects.
- **Reading the numbers correctly** — alert thresholds, the audit score's deduction caps, why average position counts a keyword outside the top 100 as 100, and why a single AI-visibility absence isn't evidence.
- **Worked workflows** — weekly review, audit triage, link-gap prospecting, AI-visibility audit, keyword expansion, new-project setup.

It also names the behaviours that read as failures but aren't: an empty new project whose first run is still working, a not-found that means wrong organization rather than deleted, and a refused second audit that's a guard rather than an error.

## Structure

```
surnex/
  SKILL.md                      the guide loaded into context
  references/
    tools.md                    all 95 MCP tools, flagged for writes and cost
    workflows.md                worked recipes
    interpreting.md             what the numbers mean and how they mislead
    troubleshooting.md          auth failures, refusals, empty results
```

`SKILL.md` is what an agent reads first. The references are loaded on demand, so the always-on context stays small.

## Contributing

The skill tracks the product. When behaviour changes — a new tool, a changed default, a limit — update the skill in the same change.

Two rules keep it useful:

- **Say what's true, including what's awkward.** A skill that overstates what the tools can do teaches an agent to promise things the product won't deliver.
- **Prefer the non-obvious.** An agent can read a tool description. It can't infer that creating a project collects nothing, or that competition is a paid-search metric.

## Related

- [Surnex documentation](https://docs.surnex.io)
- [MCP setup](https://docs.surnex.io/mcp/connect)
- [MCP tool reference](https://docs.surnex.io/mcp/tools)

## License

MIT
