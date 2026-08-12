# Authority

The line between what the agent does and what it proposes. Read by every security skill.

**Default posture: read-only.** Every containment or remediation action is proposed with the exact command, and a human executes it. An agent that isolates the wrong host during a false positive has caused the outage the alert only threatened.

## In scope to query

Systems the agent is authorised to read from at all:

- {Platform / index / console}

Anything not listed is out of scope. Querying an unauthorised system creates an incident rather than investigating one.

## Propose only — never execute

The default list. Each is proposed as a ready-to-run command with the affected entity named:

- Host isolation or network quarantine
- Disabling, locking, or resetting an account; revoking sessions or tokens
- Blocking an address, domain, or sender
- Deleting or quarantining mail
- Killing processes, deleting files, or modifying anything on a production host
- Any change to a live detection rule, firewall policy, or access grant

## Delegated actions

Actions the agent **may** take unaided, if any. Empty is a valid and recommended answer.

- {action} — authorised by {name}, on {date}, bounded to {scope}

## Escalation

- **Suspected compromise**: {path}
- **Out of hours**: {who can authorise containment, and how they are reached}
- **Standing deciders for risk acceptance**: {roles with the authority to accept a risk — read by `/risk-call`}

## Rules for consumers

- **Proposing is not asking permission by implication.** State the action, the entity, the blast radius, and what it will not fix — then stop.
- **Urgency does not promote a proposal to an action.** An active incident is when a wrong containment does the most damage.
- **A delegated action stays inside its recorded bound.** Authorisation for one host, tenant, or time window does not extend to the next.
