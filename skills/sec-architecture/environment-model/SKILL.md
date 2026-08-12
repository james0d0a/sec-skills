---
name: environment-model
description: Build and sharpen the environment model in SECURITY-CONTEXT.md — crown jewels, trust boundaries, telemetry sources and their gaps, baseline oddities, and escalation owners — capturing each fact inline as real work reveals it. Use when a security answer depends on how this environment actually works, when a baseline oddity or telemetry gap is discovered, or when another skill needs to maintain the environment model.
---

# Environment Model

Without a model of *this* environment, security reasoning goes generic: an agent enumerates what attackers usually do, hedges every conclusion, and re-learns the same three local facts on every engagement. The cure is the same one `CONTEXT.md` applies to a codebase — a small, opinionated document that makes the local truth cheap to load.

`SECURITY-CONTEXT.md` is that document. Its value is entirely in the facts a newcomer could not derive: the crown jewels, where trust actually breaks, what the telemetry cannot see, and which alarming-looking thing happens every night at 02:14.

**This is the *active* discipline** — challenging a claim about the environment, verifying it against telemetry, and writing it down the moment it settles. Merely reading `SECURITY-CONTEXT.md` for context is not this skill; any skill can do that in one line.

## Sharpen inline, never in a sprint

The model is built as a by-product of real work — an investigation, a hunt, a rule's noise pass, a design review — and never in a documentation session.

A documentation sprint produces a generic threat inventory that is wrong in the specifics and stale in a month. Real work produces the opposite: few facts, each paid for in surprise. When a hunt discovers that the EDR does not cover the build fleet, that sentence is worth more than a page of architecture diagram, and it must be written down **before the hunt's conclusion is delivered**, not queued for later.

So: capture at the moment of surprise. Never batch.

## What belongs in it

The five sections, and the format for each, are in [SECURITY-CONTEXT-FORMAT.md](./SECURITY-CONTEXT-FORMAT.md). Read it before writing to the file.

What does **not** belong: incident narratives, indicator lists, findings, remediation plans, and generic threat-actor descriptions. It is a model of the environment, not a case file and not a threat report. Anything with a date in it is probably a finding — send it to the investigation or the ticket, not here.

## During real work

### Catch yourself reasoning generically

When you are about to write "attackers commonly…" or "you should verify whether…", that is the trigger. Either the model answers it — read the file — or the model is missing a fact, and the fact is worth chasing now with a query and then recording.

### Verify before recording, and tier what you cannot

Every entry carries an `/evidence` tier. A telemetry source is `[Assumed]` until you have queried it and seen its fields populated; an owner is `[Reported by X]` until they answer. A model full of untiered assumptions is more dangerous than no model, because the next reader treats it as ground truth.

Turning an `[Assumed]` entry into an `[Observed]` one, or deleting it, is real work — do it when the entry blocks a conclusion.

### Challenge conflicts immediately

When the user asserts something the model contradicts, say so on the spot: *"the model records the file server as the crown jewel and the DB as a replica — you are treating the DB as primary. Which is it?"* One of the two is wrong, and finding out which is usually five minutes and always worth it.

The same applies when telemetry contradicts the model. The model loses to the query, every time — then gets corrected.

### Sharpen "normal"

A baseline oddity is only useful with its **cause and its owner**. "The 02:14 logon is expected" saves nobody: the next analyst cannot tell whether an 02:20 logon is the same thing. Record what runs, on what schedule, as which principal, owned by whom.

An oddity with no known cause is not an oddity — it is a lead. Hand it to `/investigate` rather than recording it as normal.

### Keep the trust boundaries honest

Boundaries are where the environment's assumptions live, so record where trust **actually** breaks, not where the architecture diagram says it does: the flat segment behind the firewall, the shared service account that spans two tiers, the vendor with a persistent tunnel. When a boundary turns out to be enforced by convention rather than by a control, that sentence is the single most valuable line in the file.

### Record hard-to-reverse decisions as ADRs

When work produces a security decision that is hard to reverse, surprising without context, and the result of a real trade-off, write an ADR beside it — status, context, decision, consequences, in whatever `docs/adr/`-style location the repo already uses. The `/threat-model` skill carries the full format.

The model holds what *is*; the ADR holds why you chose it. Keep both out of each other: accepted-risk decisions belong in `/risk-call`'s register, not in the model.

## Completion criterion

Before delivering the work that surfaced it: every fact about the environment that surprised you during this session is either **written into `SECURITY-CONTEXT.md` with a tier**, or deliberately rejected as not-durable. A surprise you had to re-derive is a surprise you will re-derive again.
