# Telemetry

Where the data lives and how to reach it. Read by `/investigate` (Phase 1, building a pivot loop), `/hunt` (Phase 2, coverage assessment), and `/detection-engineering` (Phase 2, producing a red case).

Fill in one block per platform. Delete the examples.

## Platforms

### {Platform name}

- **Holds**: {what event types, from what population}
- **Access**: {MCP server name / CLI command / API / "analyst exports on request"}
- **Query language**: {SPL, ES|QL, KQL, SQL, jq over CSVs…}
- **Retention**: {window}
- **Population**: {which hosts, tenants, accounts are actually covered}
- **Known gaps**: {what it cannot see — or `unassessed`}

<!-- Example:

### SIEM (Splunk)

- **Holds**: EDR process events, DNS resolver logs, IdP authentications, proxy logs
- **Access**: `splunk` CLI via `scripts/spl.sh`; no MCP server
- **Query language**: SPL
- **Retention**: 90 days hot, 400 days frozen (frozen requires a restore request — hours, not minutes)
- **Population**: all servers; laptops only for IdP events
- **Known gaps**: no agent on the build fleet (`ci-*`); proxy logs exclude direct-to-internet egress from the DMZ

-->

## No-programmatic-access sources

List sources reachable only through a human or a console. `/investigate` plans around these instead of inventing a query.

- **{Source}** — {how to get data out, and the realistic turnaround}

## Rules for consumers

- **A source's `Known gaps` line bounds every conclusion drawn from it.** `unassessed` is not the same as "none", and must not be read as coverage.
- **Retention bounds the window, not the question.** If the activity may predate the window, that is a stated unknown, not a clean result.
- **Query at the aggregate first.** Overview → zoom → details-on-demand: the platform is the compute layer, the context window is the reasoning layer. Never pull unaggregated rows over a broad window.
- **This file is a starting point, not ground truth.** It was written from an interview. Anything load-bearing gets verified against a query and recorded in `SECURITY-CONTEXT.md` as `[Observed]`.
