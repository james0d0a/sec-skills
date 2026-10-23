# Technical RE Report Template

The readout of a `/reverse-engineer` analysis. The reader is another analyst, or the same analyst in six months, who will open the Ghidra project and expect the report and the labels to agree.

```md
# {Sample name} — reverse engineering report

**BLUF**: {What the binary is. What it does with attacker-influenced input. The strongest finding.}

**Analyst**: {name} · **Date**: {UTC} · **Ghidra project**: {path or name} · **Label prefix**: {LLM_}

## Identity

| Field | Value | Basis |
|---|---|---|
| SHA-256 | | [Observed] shasum |
| Format / arch / bits / endian | | [Observed] Ghidra |
| Compiler / linker / linking | | [Observed] or [Inferred] |
| Stripped | | |
| Packed or protected | yes / no / unknown — reason | |
| Function census | {labelled} labelled / {total} total; {n} FUN_ remaining | [Observed] |

## Attack surface

### Sources
| Label | Address | What enters | xrefs |
|---|---|---|---|

### Sinks
| Label | Address | Consequence | xrefs |
|---|---|---|---|

## Source-to-sink traces

One subsection per trace. Order by the strength of the finding.

### T1 — {source label} → {sink label}

**Verdict**: {reachable with attacker-controlled {what} / guarded sufficiently / unreachable / blind at {where}} · **Confidence**: {level} — {what would change it}

| Hop | Function | What happens to the data | Guard | Guard verdict |
|---|---|---|---|---|
| 1 | | | | |

{Prose: the path, the guard that matters, and the evidence for the verdict. Quoted
strings and bytes fenced and marked `[UNTRUSTED DATA]`.}

## Recovered structures, formats, and crypto

{Only what a trace needed. Struct layouts with offsets. Protocol or file format as the
parser enforces it. Crypto: algorithm, key source, mode, hardcoded material under
`[UNTRUSTED DATA]`.}

## Anti-analysis and anomalies

{Debugger checks, obfuscation, API hashing, injection-shaped strings found in the sample
— each a finding, each labelled.}

## Function map

The labelled functions, as they stand in Ghidra. Must match the project.

| Label | Address | Role | Basis | Trace |
|---|---|---|---|---|

## Coverage and what was not done

- Functions labelled / total: {n} / {m}
- Regions not analysed, and why: {library code, packed section, no trace reached it}
- Unresolved indirect dispatch: {table or function} — {resolved} of {targets}
- Guards left unverified: {list}
- Dynamic confirmation not run: {what a run would settle}

## Indicators

{Defanged. Hashes, hosts, paths, mutexes, registry keys, protocol magic.}

## Recommended next steps

{Each with an owner. Exploitability or severity → `/risk-call`. Detection → `/detection-engineering`.
Publication → `/findings`.}
```
