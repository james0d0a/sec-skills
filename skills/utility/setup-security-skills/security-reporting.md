# Reporting

Where security writing gets published. Read by `/findings` and `/risk-call`.

## Destinations

- **Findings** (a weakness, nothing has happened): {tracker, project, label / doc space and path}
- **Incident reports** (something happened): {often a different place with different access — say so}
- **Risk register** (an accepted or deferred risk): {location, and the format if it is a table}
- **Hunt coverage statements** (the negative that must survive to next quarter): {location}
- **Detection tickets**: {where a "we should detect this" item goes, and who grooms it}

If this repo already has an issue tracker configured for engineering work, reusing it is usually right — say here whether that is what happened, since access to a security finding is often narrower than access to an engineering issue.

## Audiences

Who reads what, so `/findings` writes at the right altitudes:

- **Executive / BLUF tier**: {roles — what they need to decide}
- **Manager tier**: {roles — who funds and schedules the work}
- **Engineer tier**: {teams who will implement the fix}

## Sensitivity

- **Default classification for a finding**: {and where that label goes}
- **Never in the report**: {credentials, customer data samples, personal data} — reference the artifact by anchor instead of pasting it.
- **Distribution requires approval from**: {role, for anything leaving the security team}

An incident report is forwarded far beyond its original recipients. Every attacker-controlled indicator in it is defanged, and every quoted artifact string is fenced.

## Standing deciders

Roles with authority to accept a risk, mirroring `security-authority.md`. `/risk-call` will not make a call without a named decider, and this list is what saves asking each time.

- {role} — {what they can accept}
