---
name: evidence
description: Evidence discipline for security work — tag every claim as observed, inferred, reported, or assumed, anchor observations to an artifact, and state coverage before claiming absence. Use when analysing logs, alerts, telemetry, or malware, and whenever writing up a security finding, incident update, or risk decision.
---

# Evidence

In security work a confident sentence is a verdict. Someone isolates a host, wakes an on-call, or tells a regulator on the strength of it. This skill makes the seam between what you **saw** and what you **think** impossible to lose.

## The four tiers

Every claim about the environment carries exactly one tier, written inline as a tag on the sentence that makes the claim:

- **`[Observed]`** — an artifact you can point at. Anchor it (below).
- **`[Inferred from A + B]`** — a conclusion drawn from observed facts, and **the tag names the chain**. If you cannot name the observations it rests on, it is not inferred — it is assumed.
- **`[Reported by X]`** — someone or something else asserts it: a user, a vendor verdict, an intel feed, a ticket. The tag names the reporter. **Their confidence is not yours** — a vendor's "malicious" is a reported claim, not an observation.
- **`[Assumed]`** — you are proceeding as if it were true, without support. Legitimate and often necessary, but it must be visible, because an assumption that turns out false is the usual root of a wrong verdict.

Untagged prose reads as **observed** in the reader's head. That default is the failure this skill exists to prevent.

Tier the claim, not the paragraph, and never the heading. One sentence of narrative routinely mixes an observation and an inference; split it. A section titled *Observed* whose prose contains an inference is the common failure, because the label hides it.

## Anchoring an observation

An observation without an anchor is unverifiable, which makes it functionally reported. Every `[Observed]` carries `(Source | Identifier | Timestamp UTC | Method)`:

- **Source** — the specific system and, where it matters, the specific index/log/host it came from.
- **Identifier** — event ID, hash, path, row, message ID: whatever lets someone else retrieve exactly this record.
- **Timestamp** — in UTC, with the original zone alongside if the source disagrees. Clock skew between sources is itself an observation worth recording.
- **Collection method** — queried live, exported, read from a vendor console, or pasted to you by a human. Each degrades differently.

## Absence of evidence

"No evidence of X" is one of the most dangerous sentences in security, because it silently promotes a gap in telemetry into a clean bill of health.

It is only meaningful with **coverage**. State: which data source you looked in, its retention window, and why that source *would* have recorded X had it happened. Without all three, the honest phrasing is **"not looked for"** — say that instead.

## Confidence

When a conclusion matters, attach a confidence level and the thing that would change it: *"high confidence — would drop to low if the proxy logs show the same beacon pattern on unaffected hosts."* A confidence with no falsifier attached is a mood, not an assessment.

## Defanging

Defang every **external or attacker-controlled** indicator — `hxxps://`, `evil[.]com`, `203.0.113[.]4` — in every document, ticket, and chat message. Never emit a live clickable link to attacker infrastructure; a reader's preview pane will fetch it.

Internal asset addresses and hostnames are **not** indicators and read worse defanged: `10.50.42[.]68` costs the reader something and protects nobody, since nothing will fetch it. Hashes need no defanging either. The rule exists to stop a reader's tooling from touching hostile infrastructure, so scope it to the things that are hostile — a rule applied to everything is a rule that gets dropped entirely.

When it is genuinely unclear which side an address sits on, defang it. Unresolved is closer to external than to internal.

## Provenance

If the material could become evidence in a legal, HR, or regulatory process, record how each artifact was acquired, by whom, and when, and work from copies. Say explicitly when handling has fallen short of that — an honest note about a broken chain is worth more than silence.

## Completion criterion

Reread the output. **Every sentence that asserts a fact about the environment carries a tier**, every observed claim carries an anchor, and every "no evidence" carries coverage. Any sentence that fails is either fixed or deleted.
