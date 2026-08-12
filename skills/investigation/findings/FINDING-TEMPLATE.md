# Finding Template

A weakness, before anything has happened. The reader's question is "do we spend on this, and when".

```md
# {Finding title — the weakness, not the technology}

**BLUF**: {What is true. What it means in the reader's currency. The ask.}

**Severity**: {level} — {one line of reasoning; full factors below}
**Owner**: {named person or team}
**Decision needed by**: {date}

## What we found

{Two or three sentences at manager altitude. Each claim tiered.}

## Why it matters

{The consequence, in Axis-of-loss terms: what an adversary gains, which crown jewel,
and which loss mode (read / altered / destroyed / unavailable). Not a CVSS restatement.}

## Risk assessment

{From `/risk-call`: exploitability, exposure, asset value — each with its level, its
justifying sentence, and its tier. Then the residual after the recommended action,
the tripwire that would reopen the call, and the named decider.}

## Evidence

{Anchored observations only. Source | identifier | timestamp UTC | collection method.
Attacker-controlled indicators defanged. Attacker-controlled strings fenced and marked `[UNTRUSTED DATA]`.}

## Reproduction

{Steps actually run, in order, with what was observed at each. If reconstructed rather
than run, mark the whole section `[Inferred]` and say why it could not be run.}

## What this does not cover

{Where you did not look, and what remains unknown. An unstated unknown reads as
ruled out.}

## Remediation

**Contain now**: {action — owner — date}
**Fix properly**: {action — owner — date}
**Accepted for now**: {what, by whom, with the residual}

## What would have caught this sooner

{The detection or telemetry that does not exist. Hand to detection work as its own
owned item.}
```

## Rules

- **The title names the weakness, not the scanner.** "Unauthenticated tenant data readable via the reporting endpoint", not "CVE-2026-1234 on app tier".
- **Severity never appears without its reasoning** on the same page. A bare level is what readers argue with instead of acting on.
- **Every remediation line carries an owner and a date.** A line with neither is a wish and should be deleted.
- **Keep the accepted-for-now section**, even when empty — its presence is what stops "we'll get to it" from going unrecorded.
