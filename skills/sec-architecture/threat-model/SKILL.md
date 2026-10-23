---
name: threat-model
description: A structured interrogation of a system's security design — trust boundaries, abuse cases, and the assumptions that would invalidate it — recording decisions as ADRs.
disable-model-invocation: true
---

# Threat Model

An interrogation of a **system** rather than a feature, run as a live session with the user. Use the `/environment-model` skill to keep `SECURITY-CONTEXT.md` sharp as the session goes, and the `/evidence` skill so that "the gateway validates the token" is tiered rather than assumed into truth.

Security review drifts into a vibe check when it is not walked along fixed axes. Walk these, one question at a time, and do not leave an axis until it has produced either a decision or a written assumption.

## How to run the session

**One question at a time, and wait for the answer.** A batch of six questions gets one skimmed reply that addresses the easiest of them. The point is to reach the thing the user has not thought about, and that only happens by following their last answer.

- Ask the question the previous answer opened up, not the next one on your list. The axes set the territory; they do not set the order within an axis.
- **Do not accept a category as an answer.** "We validate input" is a category. What validates it, where, and what happens to the request that fails — those are answers. Push until the answer names a mechanism you could go and read.
- When an answer reveals that nobody knows, stop pushing and write it down as an assumption. An unanswerable question is a result, not a failure — Axis 6 is built from them.
- Record as you go. A session reconstructed from memory afterwards loses exactly the surprising parts that justified running it.

## The axes

1. **What is worth taking** — the crown jewels in *this* system, and what "loss" means for each: read, altered, destroyed, or merely unavailable. A system with nothing worth taking needs no further axes.
2. **Where trust breaks** — every boundary a request or a piece of data crosses: process, network, tenant, account, third party, human. For each, what enforces it, and whether the enforcement is a control or a convention.
3. **Who the adversary is here** — the ones the system is actually exposed to: an unauthenticated internet, an authenticated customer reaching for another tenant, an insider, a compromised dependency, a stolen credential. Rank them by exposure, and say which are out of scope on purpose.
4. **Abuse cases** — for each ranked adversary, walk the paths they take. Not "SQL injection" as a category, but "the reporting endpoint takes a filter string and the service account it runs as can read every tenant". Push each path to its consequence in Axis 1's terms.
5. **What would tell you** — for each abuse case that survives, whether the environment would see it happen. An abuse case with no detection and no prevention is the finding; where coverage is unclear, that is a `/hunt`.
6. **Assumptions that would invalidate the design** — the beliefs the whole model rests on: the WAF is in front, the segment is enforced, the token is short-lived, the vendor cannot reach production. Each one is a single sentence that, if false, moves an abuse case from theoretical to live.

## Closing out

Each abuse case leaves the session in exactly one state, and the states are the deliverable:

- **Mitigated** — the control that stops it is named and confirmed to exist.
- **Accepted** — a decision, not a shrug: the reasoning is written down and a decision-maker is named.
- **Open** — work to be done, with an owner.

An open abuse case whose exploitability is disputed is a test, not a debate — hand it to `/pentest-roe` as a checklist item.

Then, before finishing:

- Record decisions that are hard to reverse, surprising without context, and the result of a real trade-off as ADRs (format below). A decision that is none of those three does not need one.
- Push everything durable about the environment — boundaries that turned out to be conventions, telemetry gaps, crown jewels nobody had named — into `SECURITY-CONTEXT.md` as you go.
- List the invalidating assumptions from Axis 6 together, as the thing to re-read when the architecture changes. That list is what makes the model worth revisiting instead of redoing.

Done when every abuse case is mitigated, accepted, or open with an owner, and no axis was skipped in silence.

## The ADR format

One file per decision, in whatever `docs/adr/`-style location the repo already uses. Numbered, titled with the decision itself rather than the topic — `0004-terminate-tls-at-the-mesh-not-the-edge`, not `0004-tls`.

- **Status** — proposed, accepted, or superseded by <n>.
- **Context** — the forces that made this a real trade-off, including the security ones. Tier any claim about the environment per the `/evidence` skill.
- **Decision** — what was chosen, in the active voice, and who chose it.
- **Consequences** — what this costs and what it forecloses, including the abuse cases it leaves open. This section is why the file is worth writing; an ADR with only upside in it was written to justify, not to record.

The model holds what *is*; the ADR holds why it was chosen. Keep accepted-risk decisions in `/risk-call`'s register rather than here — an ADR records a design choice, not a risk someone is carrying.
