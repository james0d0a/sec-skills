---
name: detection-engineering
description: Red-first discipline for detection rules — generate the telemetry that should trip the rule and watch it fire before the rule counts as written, then a noise pass with real numbers, stated evasions, and a named owner. Use when the user wants to write, tune, or review a detection rule, alert, signature, or analytic, or asks how to detect a technique.
---

# Detection Engineering

A rule nobody has watched fire is not a detection. It is a hypothesis about a query language, and roughly half of them are wrong — a field that is empty in practice, a log source that normalises the value away, a condition that can never be true on real data.

So this skill is the red → green loop of testing, transposed to telemetry. **Red** is the positive case in the platform with the rule silent. **Green** is the same case with the rule firing. A rule that has not been observed going red to green is unwritten, regardless of how finished the YAML looks.

Run the `/evidence` skill throughout: "the rule fires" is an observed claim and needs an anchor — the alert ID and timestamp. Samples and captured traffic used to build the red case are handled under the `/untrusted-artifacts` skill.

If the environment keeps a `SECURITY-CONTEXT.md`, read it for telemetry sources, their gaps, and the baseline oddities that will become this rule's false positives. Where rules live, how they deploy, and which of the four red-case routes in Phase 2 actually exist here should have been provided in `docs/agents/security-detections.md` — run `/setup-security-skills` if not.

## Phase 1 — Detect the behaviour, not the artifact

Pick the rung of the **pyramid of pain** you are aiming at, and say so. A hash or a domain is trivially changed; a tool's command-line shape costs the adversary a rebuild; the behaviour itself costs them a tradecraft change.

Detecting the low rungs is legitimate — it is cheap and it works today — but a rule aimed at a hash must be labelled as such, so nobody mistakes its coverage for the behaviour's.

State: the behaviour, the rung, and the specific observable the rule will key on.

Done when someone could break the rule on purpose and you could predict how.

## Phase 2 — Red: produce the case first

**This is the gate.** Before the rule exists, produce telemetry that the rule *should* trip, in the real platform:

1. **Emulate** — run the behaviour on a lab host under the same agent and logging configuration as production.
2. **Replay** a captured artifact — PCAP, log export, or sample detonated per `/untrusted-artifacts` — through the ingestion path.
3. **Synthesise the event** only when the first two are impossible, and mark the rule as untested against real telemetry, because that is what a hand-written event proves.
4. **Mine history** — a past incident's real events in retention are the best red case available, and cost nothing to produce.

Then confirm the case is red: **point at the event in the platform, and confirm no alert exists for it yet.** Red means both halves — the telemetry is there, and the rule is not catching it.

### Completion criterion

- [ ] The positive case exists as **records in the platform**, retrievable by ID.
- [ ] The fields the rule will key on are **populated** in those records — checked, not assumed.
- [ ] Nothing fires on it today.

If the fields you planned to key on come back empty or normalised, that is the phase working. Go back to Phase 1 and pick a different observable, or record a telemetry gap and stop.

## Phase 3 — Green: write the smallest rule that fires

Write only enough logic to catch the red case, then watch it fire and anchor the alert.

Two ways this phase lies to you:

- **Tautological rule** — keyed on strings unique to your own test run: the lab hostname, the sample's hash, the exact command line you typed. It fires by construction and generalises to nothing. Expected behaviour must come from the technique, not from your artifact.
- **Green by accident** — the rule fires, but on a field or condition other than the one you intended. Confirm *which* clause matched before believing it.

Then vary the case: change what an adversary would trivially change — path, filename, casing, argument order, an intermediate process — and re-run. A rule that survives no variation is a signature for one execution, and Phase 5 must say so.

Done when the rule has been observed firing on the red case, with the alert anchored, and its behaviour under at least one variation recorded.

## Phase 4 — Noise pass with numbers

Run the rule over a window of real, known-benign production volume, wide enough to include the environment's cycles — a patch window, a month-end job, a backup schedule. A weekday afternoon proves nothing.

Report **counts, not adjectives**: hits per day, distinct hosts and accounts, and the top clusters with their causes. "Low volume" is not a noise pass.

Then state the cost in the currency the team actually spends: **analyst-minutes per week, and whose minutes**. A rule whose owner is not the one triaging it will be muted within a month.

Benign clusters found here are baseline oddities — push them to `SECURITY-CONTEXT.md` via the `/environment-model` skill rather than only into an exclusion list, so the next rule inherits the knowledge.

Tuning happens by **excluding the explained cause**, never by narrowing until the count looks acceptable. If you cannot explain a cluster, it is not a false positive — it is an unworked lead, and the user should run `/investigate` on it.

Done when hits/day, top clusters with causes, and the triage cost with a named owner are all written down.

## Phase 5 — State what it does not catch

Every rule's coverage claim is a promise someone will rely on during an incident, so bound it explicitly:

- **Evasions** — the cheapest change that breaks it, named concretely. This falls out of Phase 3's variations.
- **Population** — where the rule is blind: hosts without the agent, platforms not covered, sources with the logging level too low.
- **Preconditions** — configuration the rule silently depends on: a logging policy, a field-extraction pipeline, an enrichment feed. Each is a way the rule dies quietly.

A rule with no stated evasion has not been thought about. Write the sentence.

## Phase 6 — Ship it as something a human can act on

- [ ] **Disposition** — alert, hunt-only lead generator, or enrichment. Not everything deserves to wake someone.
- [ ] **Severity and why**, from the behaviour's position on the kill chain, not from the rule's confidence.
- [ ] **Triage line** — the first pivot the responder should run when it fires, and the two most likely benign causes.
- [ ] **Owner** — a named person or team, for both triage and tuning.
- [ ] **Coverage note** — Phase 5's evasions, blind population, and preconditions, stored with the rule where a responder will find them.
- [ ] **The red case, kept** — as a regression test. It is the only thing that will tell you the rule broke when the log pipeline changed.

The kept red case is the highest-value artifact here. Rules do not usually fail loudly; they fall silent, and silence looks exactly like safety.
