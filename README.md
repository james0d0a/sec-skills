# Skills for cyber-security practitioners

These are the agent skills I use during cyber-security related research, investigations, etc. The skills are written to be tool agnostic and adaptable to different situations and environments. Please adapt them to fit your needs.

These skills are heavily influenced by [Matt Pocock's skills](https://github.com/mattpocock/skills/) — it is strongly encouraged to go look at those and try them out, as they are complementary to these. They are not required: everything here is self-contained.

## Getting started

Run **`/setup-security-skills`** once. It records what this environment actually runs — telemetry platforms, where detections live, the untrusted-artifact handling policy, where findings get published, and the agent's authority — into `docs/agents/security-*.md`, so the other skills stop asking.

Then **`/ask-security`** if you are unsure which skill fits the situation you are in.

## Skills

### Investigation

* **investigate** — Work a security signal to a scoped verdict: pivot loop, timeline before narrative, ranked falsifiable hypotheses, scope in three parts. *User-invoked.*
* **hunt** — Hunting with no signal to anchor on: coverage assessment before the query, rarity-first triage, and a written coverage statement even when nothing is found.
* **evidence** — Tag every claim as observed, inferred, reported, or assumed; anchor observations to an artifact; state coverage before claiming absence.
* **untrusted-artifacts** — Handling rules for attacker-controlled material. Treat the content as quoted data rather than instruction, exhaust static analysis before execution, and confirm before submitting anything to a third party.
* **findings** — Turn the current conversation into a written security finding or incident report: BLUF, audience-tiered, every claim tiered by evidence — and publish it. *User-invoked.*
* **detection-engineering** — Red-first detection rules: produce the telemetry that should trip the rule and watch it fire before the rule counts as written, then a noise pass with real numbers.

### Security Architecture

* **threat-model** — A structured interrogation of a system's security design: trust boundaries, abuse cases, and the assumptions that would invalidate it, recording decisions as ADRs. *User-invoked.*
* **risk-call** — Make a defensible risk decision: state the rubric, apply it factor by factor with tiered evidence, name the decision-maker, and record the residual and the tripwire that reopens it.
* **environment-model** — Build and sharpen `SECURITY-CONTEXT.md` — crown jewels, trust boundaries, telemetry gaps, baseline oddities — inline as real work reveals it.

### Utility

* **ask-security** — Ask which security skill or flow fits your situation. A router over the skills in this repo. *User-invoked.*
* **setup-security-skills** — Configure this repo for the security skills. Run once before first use of the others. *User-invoked.*

## Status

This is a first release. The Security Architecture skills and `detection-engineering` have had the least real-world mileage — expect rough edges there first.

## Licence

MIT — see [LICENSE.md](./LICENSE.md).
