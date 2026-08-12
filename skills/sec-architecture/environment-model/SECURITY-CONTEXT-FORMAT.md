# SECURITY-CONTEXT.md Format

One `SECURITY-CONTEXT.md` at the repo root. Create it lazily — when the first durable fact is worth recording, not before.

## Structure

```md
# Security Context — {Environment Name}

{One or two sentences: what this environment is and what it protects.}

## Crown Jewels

**Customer PII store**:
Postgres cluster `pg-prod-1..3`, the only copy of customer records. [Observed]
_Loss means_: regulatory notification within 72h.
_Owner_: Platform team.

## Trust Boundaries

**Internet → app tier**:
WAF in front of `app-*`, terminating TLS. [Observed]
_Enforced by_: WAF ruleset + security group.

**App tier → data tier**:
Nominally segmented; in practice the whole `10.4.0.0/16` is flat and reachable
from any app host. [Observed — traceroute from app-3, 2026-04]
_Enforced by_: convention only.

## Telemetry

**EDR (vendor console + SIEM index `edr-*`)**:
Process ancestry, module loads, network connections. 90-day retention.
_Blind_: no agent on the build fleet (`ci-*`) or on network appliances. [Observed]

**DNS (index `dns-*`)**:
Resolver query logs, 30-day retention.
_Blind_: hosts using public resolvers directly; no response logging. [Assumed —
never verified against a known lookup]

## Baseline Oddities

**02:14 interactive logon on all `app-*` as `svc-patch`**:
Nightly patching run, orchestrated from `ops-1`, Mon–Fri only. [Observed]
_Owner_: Ops.
_Tell it apart_: outside the 02:10–02:30 window, or on a weekend, this is a lead.

## Escalation

**Suspected compromise**: on-call security → #sec-incident → CISO for anything
customer-affecting.
**Host isolation**: Platform team can isolate; requires an owner ack out of hours.
```

## Rules

- **Every entry carries an `/evidence` tier.** `[Observed]` with an anchor, `[Reported by X]`, or `[Assumed]`. An untiered entry reads as verified fact and will be trusted as one.
- **Record the non-obvious.** A newcomer can infer that production has a database; they cannot infer that the segment is flat, that the build fleet has no agent, or that 02:14 is normal. If a competent stranger would guess it correctly, leave it out.
- **Every telemetry source states its `_Blind_` line.** A source with no recorded gaps has not been examined; write `_Blind_: unassessed` and mean it.
- **Every baseline oddity states its cause, owner, and how to tell it apart** from the real thing. Without the third, the entry disarms the next analyst instead of helping them.
- **Keep entries tight.** Two or three lines. This is a model, not a runbook.
- **No dates-in-anger.** Incidents, findings, indicators, and remediation plans live elsewhere. A timestamp only appears here as an anchor on an observation.
- **Delete what has gone false.** A corrected entry is the point of the file; a stale one is the thing that gets a host cleared by mistake.
