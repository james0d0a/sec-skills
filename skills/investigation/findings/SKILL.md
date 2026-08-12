---
name: findings
description: Turn the current conversation into a written security finding or incident report — BLUF, audience-tiered, every claim tiered by evidence — and publish it.
disable-model-invocation: true
---

# Findings

This skill takes the current conversation and produces the written artifact. Do **not** interview the user — synthesise what you already have.

The failure it exists to prevent is a wall of text with no decision in it, written at one altitude for readers who need three. A report that a busy decision-maker cannot act on in thirty seconds has not been delivered, however correct it is.

Run the `/evidence` skill throughout: every claim carries a tier, every observation an anchor, every "no evidence" its coverage. Every attacker-controlled indicator is defanged — reports get forwarded, and preview panes fetch links. Anything quoted from attacker-controlled material is fenced per the `/untrusted-artifacts` skill, because your report is now a delivery vehicle for whatever was in it.

The destinations, audiences, and sensitivity rules should have been provided to you in `docs/agents/security-reporting.md` — run `/setup-security-skills` if not. Without them, write the report to a file in the repo and say plainly that it has not been published.

## 1. Name the decision and the reader

One sentence, before any drafting: **this document asks \<who\> to decide \<what\> by \<when\>.**

If there is no decision, say so at the top and make the document shorter — an informational update that pretends to ask for something wastes the reader's attention on hunting for the ask.

## 2. Pick the branch

- **Finding** — a weakness. Nothing has happened yet; the question is whether to fix it. Template: [FINDING-TEMPLATE.md](./FINDING-TEMPLATE.md).
- **Incident report** — something happened. The question is what was affected and what changes now. Template: [INCIDENT-TEMPLATE.md](./INCIDENT-TEMPLATE.md).

If both apply — an incident that also exposed a weakness — write the incident report and raise the weakness as a separate finding. One document, one decision.

## 3. Write for three altitudes, in this order

The document is read top-down by descending seniority and increasing detail. Each tier must survive being the only part read.

- **BLUF** — three sentences: what is true, what it means in the reader's currency (money, obligation, downtime, customers), and the ask. No technique names, no product names, no tool output. If the first sentence is background, it is not a BLUF.
- **Manager tier** — scope, impact, the decision and its options with costs, the owner, the date. Enough to fund or defer the work without reading further.
- **Engineer tier** — the timeline, the anchored evidence, reproduction, and the fix. Fully specified. No reassurance here; reassurance is a management-tier claim and it reads as hedging down here.

Do not launder uncertainty across altitudes. If the engineer tier says a source was blind, the BLUF cannot say "contained".

## 4. Borrow the parts that already have a discipline

- **Severity** — run the `/risk-call` skill rather than asserting a number. Copy in the factors, the residual, and the named decider. An unexplained severity is the thing readers argue with instead of acting on.
- **Scope** — if this came from an investigation, carry over its three parts verbatim: affected, ruled out, unknown. An unstated unknown reads as ruled out, and in a report that is read as a promise.
- **Reproduction** — steps you actually ran, anchored. A reconstructed repro is `[Inferred]` and must say so.
- **Remediation** — split **contain now** from **fix properly**, each with a named owner and a date. An unowned recommendation is a wish.
- **What would have caught this sooner** — the detection or telemetry that does not exist. This is often the most valuable paragraph in the document; it belongs in the report, not just in someone's memory.

## 5. Publish and close the loop

Publish to the configured tracker or report location. Then state where it went and who was told, and hand the follow-on work over as separate, owned items rather than leaving it inside the report's prose.

## Completion criterion

- [ ] The BLUF states the ask in three sentences and survives being read alone.
- [ ] Every sentence asserting a fact about the environment carries a tier; every observation an anchor.
- [ ] Every "no evidence of" carries its coverage; every unknown is named as unknown.
- [ ] Severity carries its reasoning and a named decider.
- [ ] Every remediation item has an owner and a date.
- [ ] Every attacker-controlled indicator is defanged; every quoted artifact string is fenced and marked `[UNTRUSTED DATA]`.
- [ ] The document contains a decision, or says explicitly that it asks for none.

Then read it once as the most senior person on the distribution list, who will read only the first paragraph. If that paragraph leaves them unable to act, fix it — the rest of the document is not the problem.
