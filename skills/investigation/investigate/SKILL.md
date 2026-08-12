---
name: investigate
description: Work a security signal to a scoped verdict — pivot loop, timeline before narrative, ranked falsifiable hypotheses, and scope stated as affected, ruled out, and unknown. Use when there is a signal to work: an alert, a suspicious log or process, a possible compromise, or a "was this host owned" question that needs a verdict someone can act on.
disable-model-invocation: true
---

# Investigate

A discipline for working a signal to a verdict. Skip phases only when explicitly justified.

## Phase 0 — Load the disciplines before claiming to follow them

**Invoke `/evidence` now, with the Skill tool, before Phase 1.** Invoke `/untrusted-artifacts` too the moment any artifact an adversary may have authored is in play — a capture, a log export, a sample, an email, a scraped page. That covers most investigations, so the default is to load it here as well.

This is a tool call, not a figure of speech. An investigation that paraphrases these disciplines from memory produces their surface and loses their gates: tiers land on headings instead of claims, indicators go out live, and "no evidence of X" ships without coverage. Every one of those is invisible in the finished report, which reads exactly as well either way.

### Completion criterion

- [ ] Both skills invoked, and you can name **the four tiers** and what each requires.
- [ ] You can state the anchor fields an observation needs, and the rule for indicators in written output.

If you cannot do either from what is now in front of you, you have not loaded them. Go back.

If the environment keeps a `SECURITY-CONTEXT.md` — crown jewels, telemetry sources and their gaps, known-odd baselines — read it first, and push anything durable you learn back into it via the `/environment-model` skill. `docs/agents/security-telemetry.md` holds the platforms and how to query them, and `docs/agents/security-authority.md` the line between what you may run and what you may only propose.

## Phase 1 — Build a pivot loop

**This is the skill.** Everything else is mechanical. A **pivot loop** is a query you can run, vary, and re-run against real telemetry, returning the events in question. With one, you will find what happened; hypothesis-testing, scoping, and timeline construction all just consume it. Without one you are narrating.

Spend disproportionate effort here. **Be aggressive. Be creative. Refuse to give up.**

### Overview first, zoom and filter, then details on demand

Never pull unaggregated raw records over a broad window. The telemetry engine is the compute layer and your context is the reasoning layer, so make the engine do the reduction — three steps, in order:

1. **Overview** — counts, time distributions, field cardinality. Establish what normal volume looks like without ingesting a single raw row.
2. **Zoom** — aggregate by the high-cardinality keys and pull out what is rare: clusters of one or two events, unexpected parent-child pairs, timestamps outside the working pattern.
3. **Details** — retrieve full raw records only for the anomalies step 2 isolated, and only by explicit record ID or timestamp.

A raw dump over a wide window buries the two interesting rows in fifty thousand boring ones, and you will read neither.

### Ways to construct one — roughly in order

1. **Direct query** against the log platform for the entity in question, over a window wider than the alert's.
2. **Endpoint telemetry** for the host: process ancestry, module loads, network connections.
3. **Identity trail** for the account: authentications, token issuance, privilege changes, across every IdP and application that sees it.
4. **Network view** — flow records, DNS, proxy — for the address or domain.
5. **Raw artifact collection** where live telemetry is thin: triage collection from the host, disk or memory image, mailbox export.
6. **Replay** a captured artifact (PCAP, HAR, log export) through analysis locally when the platform cannot express the query.
7. **Reconstruct from a peer** — an identical host that is *not* implicated, queried the same way, so you can diff normal against suspect.
8. **Ask the human in the loop** — the user, the asset owner, the person whose account it is. "Was this you at 02:14" resolves more alerts than any query.

Build the right pivot loop and the investigation is 90% done.

### Tighten the loop

Once you have *a* loop, tighten it: narrow the field set so results are readable, widen the time window until you see the edges of the activity, and pin the timezone so every result speaks one clock.

Then **prove the loop against a record you already know.** Point it at one event whose contents you have confirmed some other way — read from the raw source, decoded by a second tool, or already known from the signal that started this — and check that the fields land where you think they do.

A loop that extracts the wrong column does not fail; it returns confident, well-formatted, wrong answers, and every count downstream inherits the error. Log formats where the header and the data rows disagree, tools that add or drop a field between versions, and delimiters inside values are all routine. This check costs one query and it is the only thing standing between you and a verdict built on the wrong column.

### Say what counts as outside

Nearly every conclusion an investigation reaches is really a claim about a boundary — *external* scan, *internal* pivot, *outbound* channel. The loop encodes some definition of that boundary whether you chose one or not, so choose it, and write it beside the loop.

**RFC1918 is not the answer.** Real environments break it constantly: publicly routable ranges used as internal space, carrier or partner ranges that are effectively inside, NAT and proxy egress points where an internal host wears an external address, and transit traffic that terminates nowhere in the environment at all. Write down the ranges actually in use, which side each sits on, and the egress points that translate between them.

Tier the map like any other claim. On unfamiliar ground it is usually `[Reported by the asset owner]` or `[Inferred from traffic patterns]`, rarely `[Observed]` — and an untiered map reads as established fact while being the single assumption most likely to invert a verdict.

Then keep the loop and the prose honest against each other. A filter that treats an address as inside while the write-up calls it external is not a wording slip; it is two different investigations sharing one document.

If the environment already keeps a `SECURITY-CONTEXT.md`, the map is a trust-boundary fact worth adding via the `/environment-model` skill. **Do not create the file just for this** — on ad-hoc work the map belongs with the investigation it bounds, not in a document nobody will maintain.

### Name the blind spots

Before leaving this phase, state what the loop **cannot** see: sources not onboarded, retention that ends before the activity starts, endpoints without an agent, encrypted traffic without inspection. These bound every conclusion you will reach, and Phase 5 will need them.

### Completion criterion

Phase 1 is done when you can name **one query you have already run** — paste it and its output — that returns events for the entity under investigation, and:

- [ ] **Real** — it hits actual telemetry, not a description of what the telemetry would say.
- [ ] **Proven** — checked against one record whose contents you already knew, so the fields are confirmed to land where you think.
- [ ] **Variable** — you can change the entity, field, or window and re-run it, so it can answer the *next* question too.
- [ ] **Time-pinned** — one explicit timezone, and a window wider than the original signal.
- [ ] **Bounded** — its blind spots are written down.
- [ ] **Classified** — the loop encodes a definition of inside versus outside this environment, that definition is written next to it and tiered, and it is the same definition every "external" claim in the verdict will use.

If you catch yourself constructing a story about the adversary before this query exists, **stop — narrating ahead of the data is the exact failure this skill prevents.**

## Phase 2 — Timeline before narrative

Build a **timeline**: one row per observed event, in UTC, each anchored to its source. Nothing else. No motives, no actor names, no "the attacker then…".

The discipline is that a timeline is falsifiable and a narrative is not. Ordering exposes what a story papers over — the four-hour gap, the event that precedes the supposed initial access, the two sources that disagree by 90 seconds.

Then extract the **entities** the timeline touches — accounts, hosts, addresses, hashes, tokens — as the pivot list for later phases.

Both artifacts go **in the delivered output**, not just in your head. Phase 5 is exhaustive over the entity list, so a list that was never written down turns that check into a formality: everything you happened to mention is accounted for, and everything you dropped is invisible.

### Completion criterion

- [ ] A **timeline** exists in the output: one row per observed event, UTC, each row anchored to its source.
- [ ] Gaps are **marked as gaps**, with their duration, rather than smoothed over.
- [ ] No row contains a motive, an actor name, or a "then the attacker…" — those belong to the assessment, not the timeline.
- [ ] An **entity list** exists in the output, extracted from the timeline, naming every account, host, address, hash, and token it touches.

## Phase 3 — Hypothesise

Generate **3–5 ranked hypotheses** before testing any of them. Single-hypothesis generation anchors you on the first plausible story, and in security the first plausible story is usually "compromise" when the truth is usually a sysadmin.

**One hypothesis must be the benign explanation** — misconfiguration, a scanner, a backup job, an admin working late, a broken integration. State it seriously and rank it honestly. Most signals resolve here, and an investigation that cannot conclude "benign" will find an adversary whether or not one exists.

Each hypothesis must be **falsifiable**: state the prediction it makes against telemetry you actually have.

> Format: "If <X> happened, the pivot loop against <source> will show <Y>; if it did not, <Y> will be absent."

If you cannot state the prediction, the hypothesis is a vibe — discard or sharpen it.

**Show the ranked list to the user before testing.** They know that the 02:14 logon is the patching window. Cheap checkpoint, big time saver. Do not block on it if they are away.

### Completion criterion

- [ ] **3–5 hypotheses, written in the output**, ranked, before any of them is tested.
- [ ] **One is the benign explanation**, stated seriously rather than as a foil.
- [ ] Each carries a **falsifiable prediction** against telemetry you actually have, in the form above.
- [ ] The list survives into the delivered output **with each hypothesis's outcome recorded** — supported, not supported, or untested and why.

Per-finding caveats are not a substitute for this. A report that hedges each finding individually still shows only the explanations that survived; the ranked list is what shows a reader which alternatives were *tested*, on what, and where they failed.

## Phase 4 — Test

Each pivot maps to a specific prediction from Phase 3. **Change one variable at a time** — one entity, one window, one source — so a result means something.

Feed confirmed events back into the timeline as you go, and let new entities expand the pivot list. **Look actively for the disconfirming result**, not only the corroborating one; the fastest way to a correct verdict is killing your top hypothesis cheaply.

Stop when the surviving hypothesis predicts nothing further you can check with the telemetry you have. That is a conclusion, not a failure.

## Phase 5 — Scope

Before any verdict, state scope in three parts. This is the deliverable a decision-maker actually needs.

- **Affected** — entities you have observed involved, each anchored.
- **Ruled out** — entities you checked and cleared, with the coverage that makes "cleared" mean something.
- **Unknown** — everything you could not check, and why: no telemetry, retention expired, no access. Name it. An unstated unknown reads as ruled out.

Scope is where an investigation is usually wrong: the question is rarely "is this bad" and almost always "**how far did it go**".

### Account for every entity

Scope is **exhaustive over the entity list**, not a selection from it. Walk Phase 2's entity list and every entity that appears anywhere in the timeline, and place each one in exactly one of the three lists.

An entity that is interesting enough to appear in the timeline and then appears in no list has been silently dropped — and it is usually the one nobody explains: the internal host issuing writes, the pair of workstations driving the enumeration, the address you assumed was infrastructure. Dropping it is not the same as clearing it.

Untested inferences land here too. **Any inference the verdict rests on that you did not test goes in Unknown**, named as the specific open question — "whether A and B are one actor pivoting or two sources" — rather than left implicit in a finding's prose. An inference stated confidently in a finding and absent from Unknown reads as established.

### Enumerate coverage against what the evidence contains

A coverage statement about one thing reads as coverage of everything. Run one dissector across one protocol, write "no evidence of destructive commands", and the sentence silently vouches for the protocols you never touched.

So enumerate. List **every protocol, log source, and entity class actually present in the evidence**, and for each one state either what you checked — the specific dangerous operations, function codes, or event types, named — or **"not looked for"**. Both are acceptable answers. Only the missing third option is not.

This does not make you find more. It makes what you did not look for **visible**, which is the difference between an investigation with a known hole and one that reads as complete. A senior reader can act on "Modbus programming function codes: not looked for". Nobody can act on silence.

### Completion criterion

- [ ] Every entity in Phase 2's list and in the timeline appears in **exactly one** of affected, ruled out, unknown.
- [ ] Every protocol and log source present in the evidence carries either a named check or an explicit **"not looked for"**.
- [ ] Every "ruled out" carries its coverage: the source, the window, and why that source **would** have shown it.
- [ ] Every untested inference the verdict depends on is named in Unknown.

## Phase 6 — Verdict and handoff

**Lead with the verdict.** One statement of what happened, carrying a confidence level and the finding that would change it. It is the first thing in the output and it is not optional — a report that opens on evidence, or on which finding matters most, leaves the reader to assemble the conclusion themselves, and they will assemble a different one than you would.

If the honest verdict is "no adversary activity established", say exactly that, with the same confidence and falsifier. A hedged pile of findings is not a verdict.

Then deliver the rest, and write it up with `/findings` if it leaves this conversation:

- [ ] **The verdict**, with a confidence level and the finding that would change it.
- [ ] **The scope**, in the three parts above.
- [ ] **Recommended actions**, separated into *contain now* and *fix properly*, each with an owner.
- [ ] **Where the pivot loop was blind** — the gaps from Phase 1 that limited the investigation.
- [ ] **A pass against `/evidence`'s completion criterion** — reread that skill's criterion, not your memory of it, and apply it to the finished text. The failure it catches most often here is a finding titled *Observed* whose prose contains an inference.

**Do not assign severity.** An investigation establishes what happened and how far it went; severity is a decision about what to do next, and it needs a rubric, a residual, and a named decider that this skill does not collect. A `Critical` or `High` invented here looks authoritative and is unfalsifiable — the reader cannot tell what would make it a `Medium`.

If a severity is genuinely needed, run the `/risk-call` skill and carry its factors and named decider across. Otherwise name which finding matters most and why, in a sentence, **as an addition to the verdict and never as a replacement for it** — a note about which finding leads is not a conclusion about what happened.

**Then ask: what would have caught this sooner?** If the answer is a detection that does not exist, or telemetry that is not collected, that is the real output of the investigation — record it, and hand it to the `/detection-engineering` skill if it is a rule, or the `/hunt` skill if the question is how far the same behaviour reached. Make the recommendation **after** the verdict is in, not before: you know more now than when you started.
