# Detections

Where rules live and how a positive case gets produced. Read by `/detection-engineering`.

## Rule store

- **Location**: {repo path, platform-native content pack, or "in the console only"}
- **Format**: {Sigma / platform-native / code}
- **Deployment path**: {how a rule gets from written to live — PR and CI, console edit, sync job}
- **Review requirement**: {who approves before a rule goes live}

## Producing a red case

`/detection-engineering` treats a rule as unwritten until it has been observed firing on real telemetry. Which of these are available here:

- **Lab / emulation host**: {name and how to reach it, or `none`}
  - Logging configuration matches production: {yes / no / partially — say how it differs}
- **Replay path**: {can a PCAP or log export be pushed through the ingestion pipeline? how?}
- **Historical events**: {is past incident data still in retention and queryable? which window?}
- **Synthetic events**: {can events be hand-written into an index? which one?}

If all four are `none`, say so plainly here. Every rule written in this environment is then untested against real telemetry, and must be labelled that way rather than shipped as if it had fired.

## Noise baseline

- **Known-benign window to test against**: {a window wide enough to include a patch cycle, month-end, and backup schedule}
- **Population to test against**: {which hosts/tenants count as representative}

## Ownership

- **Default rule owner**: {team who triages and tunes, if the author does not}
- **Where the kept red case lives**: {alongside the rule, in a test directory, in the ticket}

The kept red case is the regression test. Rules do not usually fail loudly — they fall silent, and silence looks exactly like safety.
