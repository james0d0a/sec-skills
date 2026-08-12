# Incident Report Template

Something happened. The reader's question is "how far did it go, is it over, and what changes now".

```md
# {Incident title — what happened, plainly}

**BLUF**: {What happened. What it means in the reader's currency — customers, obligation,
downtime, money. What is being asked of them now.}

**Status**: {active / contained / closed} — {as at timestamp UTC}
**Confidence**: {level} — {the finding that would change it}
**Incident owner**: {named person}

## Evidence base and coverage

{One row per data source the conclusions rest on: what it is, the window it covers, and
its volume or extent. Then the collection method and the timezone the report speaks in.}

| Source | Window (UTC) | Extent |
|---|---|---|

{Then, in a line each: how this data was acquired, and any integrity problem found in the
data itself — a clock that disagrees, a duration that cannot be right, a merge artifact,
a gap in collection. An integrity problem in the evidence bounds every claim below it and
belongs here, not in a footnote.}

## Scope

**Affected**: {entities observed involved, each anchored}
**Ruled out**: {entities checked and cleared, with the coverage that makes "cleared" mean
something}
**Unknown**: {what could not be checked, and why — no telemetry, retention expired, no
access}

Scope is the section decision-makers actually need. The question is rarely "was this bad"
and almost always "how far did it go".

## Timeline

{One row per observed event, UTC, each anchored to its source. Gaps marked as gaps.
No motives, no actor names, no "the attacker then…" — the narrative goes in Assessment.}

| Time (UTC) | Event | Source | Tier |
|---|---|---|---|

## Assessment

{What you believe happened and why, with the chain stated: `[Inferred from A + B]`.
This is the only section where a narrative belongs, and every sentence in it is
tiered.}

## Hypotheses considered

{Every hypothesis that was ranked, including the ones that lost — with the prediction each
made and what actually happened. The benign explanation appears here by name.}

| # | Hypothesis | Prediction it made | Outcome |
|---|---|---|---|

A report that shows only the surviving explanation cannot be told apart from one that never
considered another. This table is what makes the verdict reviewable: a reader can see which
alternative was tested, on what, and where it failed — and a surviving alternative here is
the honest bound on the verdict's confidence.

## Impact

{Data, systems, and people affected. Whether notification or regulatory obligation is
triggered, and who is deciding that. Say "not yet determined" where it is not — do not
leave it out.}

## Response so far

{What was done, by whom, at what time. Including anything that went wrong or was
missed — a report that omits the fumbles teaches nobody and is usually detectable.}

## What changes now

**Contain now**: {action — owner — date}
**Fix properly**: {action — owner — date}
**Detection or telemetry gap**: {what was blind, and who owns closing it}

## Where we were blind

{The telemetry gaps that bounded the investigation. These bound every claim above,
including "ruled out".}
```

## Rules

- **Status and confidence sit in the header**, with a timestamp. A report read a day later must not imply that "contained" is still true as of now.
- **Timeline and assessment stay separated.** A timeline is falsifiable and a narrative is not; merging them is how a story becomes a fact.
- **"Contained" in the BLUF requires the coverage to support it.** If the engineer tier says a source was blind, the BLUF cannot claim containment — say "contained on observed hosts" and name the population.
- **Include what went wrong in the response.** Omitting it is the fastest way to lose the reader's trust in everything else.
- **The losing hypotheses stay in.** Deleting them on the grounds that they are not what happened removes the only evidence that the verdict was tested.
- **An integrity problem in the evidence goes above the findings, not below them.** A clock that cannot be right or a capture that cannot be that long bounds everything downstream, and a reader who meets it on the last page has already believed the report.
- **No attacker-controlled indicator appears live.** Defang every one, including in tables. Internal addresses and hashes stay intact — see `/evidence`.
