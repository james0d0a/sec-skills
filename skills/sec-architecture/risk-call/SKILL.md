---
name: risk-call
description: Make a defensible security risk decision — state the rubric, apply it factor by factor with tiered evidence, name the decision-maker, and write down the residual and the tripwire that would reopen it. Use when the user asks how bad something is, wants a severity or priority set, is deciding whether to fix, accept, or defer a vulnerability, or asks whether a risk is acceptable.
---

# Risk Call

A **call** is a decision — fix now, fix later, accept, transfer — made by a named person about a specific thing. Asking "how bad is this?" produces a score instead, and a score is not a decision: nobody can act on a 7.8, and six months later nobody can reconstruct it.

So the deliverable is the **recorded reasoning**, not the number. The number is a handle for the reasoning; if the reasoning is missing, the handle is attached to nothing.

Run the `/evidence` skill throughout — every factor below is a claim about the environment and carries a tier. Read `SECURITY-CONTEXT.md` if it exists: exposure and asset value are questions about *this* environment, and answering them generically is the failure this skill prevents.

## Phase 1 — Name the decision and the decider

State, in one sentence: **who decides what, by when.**

- **What is on the table** — the specific action, not the abstraction. "Leave the reporting endpoint unauthenticated until Q4" is a decision. "Accept the risk of the reporting service" is a mood.
- **Who decides** — a named person or role with the authority to carry the consequence; `docs/agents/security-authority.md` lists the standing deciders if it exists. If you cannot name them, you are not making a call; you are writing a recommendation, and it should say so at the top.
- **By when** — the point at which not-deciding becomes the decision, because it usually does.

Done when the sentence exists. If the answer is "nobody has to decide anything", stop — this is a finding to report, not a call to make.

## Phase 2 — Apply the rubric out loud

The rubric is **exploitability × exposure × asset value**. Each factor gets a level *and* the sentence that justifies it, tiered.

- **Exploitability** — what the adversary must already have: network position, credentials, a user's click, a race won. Whether working exploit code is public. Whether they need to be lucky more than once. `[Reported by X]` when it rests on a vendor's or researcher's claim.
- **Exposure** — where the thing sits relative to the trust boundaries that actually hold. Reachable from the internet, or from a segment that turned out to be flat, or only by someone who is already domain admin. This factor is where a generic score is most often wrong in your favour, and occasionally wrong against you.
- **Asset value** — the crown jewel behind it, and the **loss mode**: read, altered, destroyed, or merely unavailable. These are not interchangeable, and the reader's currency differs for each: notification obligations, corrupted books, downtime.

**Never import a vendor severity as the answer.** A CVSS base score is a reported claim about a generic environment, which is precisely the environment you are not in. Name at least one factor where the local answer differs from the generic one — or state explicitly that none do, which is itself a finding worth writing.

Two failure directions, both fatal to the register's credibility: **inflation**, where everything is critical and so nothing is; and **convenience**, where the score quietly tracks how cheap the fix is. Guard against the second by writing the factors before the action.

### Completion criterion

- [ ] Three factors, each with a level and a justifying sentence.
- [ ] Every sentence tiered; every `[Observed]` one anchored.
- [ ] The local adjustment away from any generic score named, or its absence stated.
- [ ] Written **before** the decision in Phase 4 was reached.

## Phase 3 — Test the compensating controls

A control only lowers a factor if it works. "The WAF blocks that" and "EDR would catch it" are `[Assumed]` until demonstrated, and assumed controls are how accepted risks turn into incidents.

The demonstration is cheap and it exists: a red case from the `/detection-engineering` skill showing the rule fires on this behaviour, or a coverage statement from the `/hunt` skill showing the telemetry could see it. Point at one, or mark the control assumed and lower the factor by less.

State each control's **failure mode** too — the config change, licence lapse, or logging level that silently removes it.

## Phase 4 — The call, the residual, and the tripwire

- **The call** — the action chosen, from Phase 1's options, with the reasoning that connects it to Phase 2's factors.
- **The residual** — what remains *after* the chosen action, written as the consequence that will actually land, in the decider's currency. "Residual: low" is not a residual. "If exploited, one tenant's report data is readable by another; notification is likely required" is.
- **The tripwire** — the specific observation that reopens the call: exploit code goes public, the segment is flattened, the asset gains a new consumer, the compensating control's failure mode fires. A call with no tripwire is a permanent decision made on temporary information.
- **The review date** — when it gets re-read even if no tripwire fires.

**When the decider overrules the analysis, record both.** Write the factors as assessed, the decision as taken, and the disagreement plainly. Retro-fitting the assessment to match the decision destroys the only thing this skill produces: a record that someone can trust later. An honest "assessed high, accepted anyway by X for Y reason" is a legitimate and common outcome.

## Completion criterion

Reread the record and ask the question it exists to answer: **six months from now, could someone who was not here tell why this was decided, and know what would change it?** Specifically —

- [ ] The action, the decider by name, and the date are all present.
- [ ] The three factors are written with their tiers and their local adjustment.
- [ ] Every compensating control is either demonstrated or marked assumed.
- [ ] The residual states a consequence, not an adjective.
- [ ] The tripwire and review date are recorded.
- [ ] Any disagreement between the assessment and the decision is visible.

Then put it where it will be found — the risk register, the ticket, the ADR — not in the chat where the call was made. If it needs writing up for an audience, say so and let the user run `/findings`.
