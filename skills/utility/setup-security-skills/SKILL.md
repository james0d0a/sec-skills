---
name: setup-security-skills
description: Configure this repo for the security skills — telemetry platforms, detection pipeline, untrusted-artifact handling policy, where findings and risk decisions get published, and the agent's authority. Run once before first use of the other security skills.
disable-model-invocation: true
---

# Setup Security Skills

The security skills are deliberately tool-agnostic: the discipline holds whether the data is in Splunk, Elastic, or a pile of CSVs. That agnosticism has a price, and this skill pays it once — recording what *this* environment actually runs, so the other skills stop asking.

Five things get recorded:

- **Telemetry** — which platforms hold the data, and how to query each
- **Detections** — where rules live, in what format, and where a red case can be produced
- **Handling** — what may be done with attacker-controlled material, and what needs a human first
- **Reporting** — where findings, incident reports, and risk decisions get published
- **Authority** — what the agent may do unaided, and what it may only propose

This is a prompt-driven skill, not a deterministic script. Explore, present what you found, confirm with the user, then write.

**Two answers here are safety decisions, not preferences** — the handling policy and the authority boundary. Do not infer either from the environment. Ask, and record the answer verbatim.

## Process

### 1. Explore

Read what exists; assume nothing:

- `CLAUDE.md` / `AGENTS.md` at the root — does either exist, and does it already carry a `## Security skills` block?
- `SECURITY-CONTEXT.md` — has the `/environment-model` skill already started one?
- `docs/agents/` — is there already an issue-tracker or publishing config here from another skill set? It may be the right publishing target.
- Any detection content already in the repo: `detections/`, `rules/`, `sigma/`, `*.yml` with `detection:` keys.
- Any configured MCP servers or CLIs that reach a SIEM, EDR, or ticketing system — these are the real query path, and finding one is worth more than any answer the user gives.

### 2. Present findings and ask

Summarise what is present and what is missing. Then walk the five sections **one at a time** — present, get the answer, move on. Do not dump all five.

Each section opens with a short explainer: what it is, which skills read it, and what changes if the answer is different. Assume the user knows security but not these skills.

**Section A — Telemetry.**

> Explainer: `/investigate` and `/hunt` are built around a query you can actually run. They need to know which platforms hold what, and by what mechanism you reach them — an MCP server, a CLI, an API, or a human who exports CSVs on request. Their coverage discipline also needs each source's retention window and the population it covers, because a negative result is meaningless without them.

For each platform the user names, capture: what it holds, how it is queried, its retention window, and the population it covers. Push for the **gaps** too — the fleet without an agent, the sampled flow records — and say plainly that a source with no recorded gaps is a source nobody has examined.

Where there is no programmatic access, record that: "exports provided by the analyst on request" is a legitimate and common answer, and `/investigate` will plan around it instead of hallucinating a query.

**Section B — Detections.**

> Explainer: `/detection-engineering` will not consider a rule written until it has watched it fire, so it needs to know where rules live, in what format, and where a positive case can be produced — a lab host, a replay path, or historical events in retention.

Capture: the rule store and format (Sigma, platform-native, code), the deployment path, whether a lab exists for emulation, and whether historical incident data is queryable. If there is **no** way to produce a positive case, say so here — the skill will then mark every rule as untested against real telemetry, which is the honest outcome.

**Section C — Handling policy.**

> Explainer: `/untrusted-artifacts` treats submitting a sample to a third party as publishing it — irreversible, and visible to the adversary. Which lookups are pre-approved versus which need a human varies per organisation, and getting it wrong discloses victim data or tips off an operator.

Ask, and record verbatim:

- **Sample storage** — where samples may live, and where they may never go.
- **Pre-approved (passive) lookups** — typically hash-only checks against internal indices or an internal intel platform.
- **Requires confirmation (active)** — public multi-scanner submission, sandbox uploads, resolving or fetching attacker infrastructure. Record whether these are *permitted with confirmation* or *prohibited outright*; both are common.
- **Detonation environment** — the isolated lab, or its absence. If there is none, record that execution is not available, so the skill stops offering it.

**Section D — Reporting.**

> Explainer: `/findings` publishes a finding or incident report, and `/risk-call` records a decision that has to be findable in six months. Both need a destination; without one they degrade to writing a file and telling you it was not published.

Capture: where findings go, where incident reports go (often different), where the risk register lives, and who the standing audiences are. If the repo already has an issue tracker configured for engineering work, propose reusing it and say so — but check the access first, since a security finding is often narrower.

Also capture the **standing deciders** — the roles with authority to accept a risk. `/risk-call` refuses to make a call without a named decider, so a role list here saves that question every time.

**Section E — Authority.**

> Explainer: security work reaches for actions that are hard to undo — isolating a host, disabling an account, blocking an address, deleting mail. The skills need a bright line between what the agent does and what it only proposes.

Default posture, and the one to recommend: **read-only. Every containment action is proposed with the exact command, and a human executes it.** Ask explicitly whether anything is delegated beyond that, and record the answer, including the escalation path for out-of-hours action.

Record the systems that are **in scope** to query at all. An agent that queries a system it was not authorised to touch has created an incident rather than investigated one.

If offensive testing happens here, also capture the standing rules `/pentest-roe` and `/pentest-run` read: who may issue the `AUTHORIZE SCOPE` handshake, where scoping documents live, standing exclusions, the default impact ceiling, and which blast-radius classes need a second confirmation. If it does not, record that offensive testing is not authorised, so `/pentest-run` refuses rather than asks.

### 3. Confirm and edit

Show a draft of the `## Security skills` block and all five docs files. Let the user edit before anything is written. Read Sections C and E back to them explicitly — those two are the ones that cause harm when wrong.

### 4. Write

**Pick the file to edit:** if `CLAUDE.md` exists, edit it; else if `AGENTS.md` exists, edit that; if neither exists, ask which to create. Never create one when the other is present. Update an existing `## Security skills` block in place rather than appending a duplicate.

The block:

```markdown
## Security skills

### Telemetry

[one-line summary of the platforms and access mechanism]. See `docs/agents/security-telemetry.md`.

### Detections

[one-line summary of the rule store and whether a red case can be produced]. See `docs/agents/security-detections.md`.

### Handling

[one-line summary — especially whether third-party submission is permitted]. See `docs/agents/security-handling.md`.

### Reporting

[one-line summary of where findings and risk decisions go]. See `docs/agents/security-reporting.md`.

### Authority

[one-line summary — read-only, or the specific delegated actions]. See `docs/agents/security-authority.md`.
```

Then write the five docs from the seed templates in this skill folder:

- [security-telemetry.md](./security-telemetry.md)
- [security-detections.md](./security-detections.md)
- [security-handling.md](./security-handling.md)
- [security-reporting.md](./security-reporting.md)
- [security-authority.md](./security-authority.md)

### 5. Seed the environment model, tiered honestly

Section A collected telemetry sources and gaps, which is exactly the Telemetry section of `SECURITY-CONTEXT.md`. Write them there, in the format the `/environment-model` skill defines — and tier every entry `[Reported by <the user>]`, not `[Observed]`.

This is the one sanctioned exception to that skill's rule against documentation sprints, and the tier is what keeps it honest: nothing here has been verified against a query yet. Real work upgrades each entry to `[Observed]` or deletes it. Say this to the user so the seeded file is not mistaken for a verified one.

Write nothing else into `SECURITY-CONTEXT.md`. Crown jewels, trust boundaries, and baseline oddities are earned during real work, and an interview produces the generic version of all three.

### 6. Done

Tell the user which skills now read which file, that `docs/agents/security-*.md` can be edited directly, and that re-running this is only needed to switch platforms or start over.

Point them at `/ask-security` if they are unsure which skill to reach for first.
