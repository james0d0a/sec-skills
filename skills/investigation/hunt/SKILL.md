---
name: hunt
description: Hunting loop for security work with no signal to anchor on — hypothesis about adversary behaviour, coverage assessment before the query, rarity-first triage, and a written coverage statement even when nothing is found. Use when the user wants to hunt, asks whether a behaviour or technique is present in the environment, wants to sweep for a TTP or an intel report's indicators, or asks "are we affected by this".
---

# Hunt

Hunting is investigation without a signal. Nothing is flagged, so nothing bounds the work, and the failure is not a wrong verdict — it is a **clean bill of health issued by a query that could never have seen the behaviour**.

So the deliverable is not "found / not found". It is a **coverage statement**: what you looked for, where, whether that source could have shown it, and what remains unlooked-at. A hunt with no leads and a coverage statement is a success. A hunt with no leads and no coverage statement is worse than not hunting, because it will be re-run next quarter by someone who trusts it.

Run the `/evidence` skill throughout — "no evidence of X" is only meaningful with coverage, which that skill defines. Intel reports, sample strings, and third-party feeds are handled under the `/untrusted-artifacts` skill.

If the environment keeps a `SECURITY-CONTEXT.md`, read it first — it holds the telemetry sources, their known gaps, and the baseline oddities that will otherwise eat the hunt. `docs/agents/security-telemetry.md` holds each platform's retention and covered population, which Phase 2 needs directly.

## Phase 1 — Hypothesis about behaviour

A hunt starts from a claim about what an adversary would **do**, not from a data source. "Look at the DNS logs" is not a hypothesis. "An operator with a foothold would stage archives before exfiltration" is.

State it at behaviour level, then derive the **observables** — the specific artifacts that behaviour must leave: a process relationship, a registry write, an authentication pattern, a payload size distribution.

Where the hypothesis comes from: an intel report, a technique the environment has never tested, a gap named by a previous investigation, a peculiarity noticed in the baseline, a crown jewel and the shortest path to it.

Done when the hypothesis names a behaviour and at least one observable it must produce. If you cannot name an observable, the hypothesis is unhuntable — sharpen it or drop it.

## Phase 2 — Coverage assessment

**This is the gate, and the whole skill.** Before writing a query, decide for each observable whether the telemetry can see it at all. Three verdicts, and you must pick one per observable:

- **Visible** — the source records this observable, in the window you need. Say which source, which field, and what retention it holds.
- **Partly visible** — recorded on some of the population, some of the time. Name the fraction and the shape of the hole: agents on servers but not laptops, verbose logging on one tenant, sampled flow records.
- **Blind** — the source cannot produce this observable. No agent, no inspection of encrypted traffic, logging level too low, retention expired before the window opens.

A **blind** observable ends the branch and becomes the hunt's output: a telemetry gap finding with the behaviour it hides. Do not substitute an adjacent source and quietly continue — that substitution is the failure this skill exists to prevent.

Write the coverage assessment down **now**, before results exist and can flatter it.

### Completion criterion

- [ ] Every observable from Phase 1 carries **visible**, **partly visible**, or **blind**.
- [ ] Each verdict names the source, the field or record type, and the retention window.
- [ ] Every **partly visible** verdict quantifies the population it misses.
- [ ] Blind observables are recorded as gap findings, not silently replaced.

If you catch yourself writing a query before this assessment exists, **stop** — you are about to produce a negative result you cannot interpret.

## Phase 3 — Query rarity, not badness

You have no signal, so you cannot filter for bad. Filter for **rare**, then explain what is left.

Treat the telemetry engine as the compute layer and your context window as the reasoning layer. Overview → zoom → details-on-demand:

1. **Overview** — count and distribute across the whole population to establish normal volume. Never pull unaggregated rows over a broad window.
2. **Stack** — aggregate by the observable's key and sort ascending. The interesting rows are the ones with a count of one or two, the parent-child pair seen on a single host, the user agent used once.
3. **Details-on-demand** — pull full records only by ID, only for the clusters isolated in step 2.

Hunt across the **whole population** the hypothesis applies to, not a sample. If you sample, the sample is part of the coverage statement.

Done when the stack has been walked to the point where remaining rows are individually explainable, not when a scroll of output has been skimmed.

## Phase 4 — Triage each cluster to a resting place

Every cluster from Phase 3 lands in exactly one of three places, with a reason:

- **Benign, explained** — you can name the cause: a scanner, a backup job, a patching window, a specific admin. An unexplained cluster is never benign; "probably normal" is not a resting place.
- **Baseline oddity** — benign, surprising, and going to bite the next hunt. Push it into `SECURITY-CONTEXT.md` via the `/environment-model` skill so it is explained once rather than re-triaged forever.
- **Lead** — you cannot explain it and it is consistent with the hypothesis.

A lead is a signal, and a signal is investigation work, not hunting work: stop hunting the lead and tell the user to run `/investigate` on it, handing over the cluster, the query, and the coverage assessment. Continuing to pivot inside a hunt is how the remaining hypotheses go unhunted.

## Phase 5 — Write the coverage statement

The artifact, produced whether or not anything was found:

- [ ] **Hypothesis and observables** — what behaviour was hunted, in behaviour terms.
- [ ] **Coverage** — per observable: source, verdict from Phase 2, retention, population covered.
- [ ] **What was run** — the queries themselves, and the window, so the hunt is repeatable.
- [ ] **Results** — leads handed off, clusters explained benign, baseline oddities recorded.
- [ ] **The negative, stated honestly** — for each observable that came back empty: *"we would have seen this if it happened, because \<source\> records \<field\> for \<population\> over \<window\>"*. Where you cannot say that sentence, the finding is **not looked for**, not clean.
- [ ] **Gaps worth fixing** — blind observables, ranked by the behaviour they hide.

**Then ask: should this be a detection instead?** A hunt that found something explainable and repeatable is a rule waiting to be written — run the `/detection-engineering` skill. A hunt you would want to re-run on a schedule is either a detection or an automation gap, and saying which is the last thing this skill owes.
