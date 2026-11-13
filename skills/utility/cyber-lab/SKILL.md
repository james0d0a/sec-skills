---
name: cyber-lab
description: Ground-truthed cyber lab development, scenario authoring/execution, and dataset evaluation — stack-agnostic across virtualized, container, cloud, and physical hardware labs. Use when building or hardening a lab, designing or running a scenario, or verifying a captured dataset against ground truth.
---

# Cyber Lab

A lab without independent ground truth grades itself. A dataset without real-vs-synthetic provenance misleads every detection, analytic, or model tested against it.

This skill governs three workflows — **Build Lab**, **Run Scenario**, and **Evaluate Dataset** — under one set of invariants. It is deliberately stack-agnostic: it works whether the lab runs on local VMs, containers, cloud ranges, or physical hardware benches.

Run `/evidence` throughout: every observation about captured telemetry, clock skew, or leaks must be anchored to an artifact, and every absence claim must state its coverage. Handle any attacker-controlled payloads or captured hostile traffic under `/untrusted-artifacts`.

---

## Step 0 — Role-based discovery

Before proposing changes or running checks, read what exists in the repo (`README.md`, `CONTEXT.md`, `SECURITY-CONTEXT.md`, provisioners, scenario scripts, dataset manifests) and map the environment onto six stack-agnostic **lab roles**:

1. **Control plane** — how targets are provisioned, orchestrated, and reset (e.g., out-of-band management network, hypervisor/container exec, serial/console, or pre-window manual setup).
2. **Scenario plane** — where targets, users, and background/adversarial traffic live.
3. **Capture point** — passive observation point(s) recording telemetry (e.g., bridge capture, hardware TAP/SPAN, host log export) that never participate as an asset.
4. **Reset mechanism** — how clean baseline state is restored between runs (e.g., VM snapshots, container rebuild, config restore, or documented manual rollback).
5. **Clock source** — how time is synchronized across driver, targets, and capture points.
6. **Telemetry & ground-truth formats** — what the lab emits (PCAP, EVTX, Sysmon, Zeek, directory exports, etc.) and where ground truth lives (CSV, JSONL, YAML).

Ask only for roles that are missing or ambiguous. If a role is absent (e.g., no automated reset or no out-of-band control plane), surface the risk explicitly before proceeding.

---

## Lab Invariants

These hold across all three workflows:

- **Control-plane isolation:** Management, provisioning, and driver traffic must not contaminate captured scenario telemetry. Prefer an out-of-band control plane and deterministic reset. When hardware or topology forces a shared medium, enforce **temporal gating** (provision/reset before capture starts), run a **leak check** afterward, and document any residual contamination as a known gap.
- **Capture neutrality:** A capture point observes; it never participates as a scenario asset.
- **Ground-truth independence:** Record what the operator or automation driver *actually executed and intended* (timestamps in UTC, actor, source, target, action, expected outcome) — never reconstruct ground truth from the captured logs. When actions are executed manually on hardware, record them with explicit `/evidence` tiers.
- **Real-vs-synthetic honesty:** Every dataset must state what is real, what is emulated or synthetic (e.g., emulated devices, synthetic clocks, placeholder directory objects), and what telemetry or protocol gaps exist.

---

## Workflow 1 — Build or Harden Lab

Use when standing up a new lab or changing topology, isolation, provisioning, or reset.

1. **Define zones and boundaries:** Map the scenario plane's segments and trust boundaries. If `SECURITY-CONTEXT.md` exists, keep it aligned via `/environment-model`.
2. **Place capture points:** Position passive capture so every relevant flow/event is observed without duplicating or injecting traffic. If a routed flow crosses multiple capture points, define an explicit deduplication rule.
3. **Isolate the control plane:** Separate management/orchestration traffic from the scenario plane (firewall/netfilter rules, dedicated management VLAN, or temporal gating if hardware shares a medium).
4. **Establish clock sync:** Point targets, capture hosts, and the driver at a shared time source, and note how skew will be measured.
5. **Build preflight and reset:**
   - **Preflight gate:** Idempotent check before any scenario run verifying reachability, required services/emulators, standing sessions, and clock skew within tolerance.
   - **Reset:** Repeatable return to a known clean baseline.

**Done when:** Preflight passes, control-plane isolation is verified (or temporal gating + leak check is documented), and reset restores clean state.

---

## Workflow 2 — Author or Run Scenario

Use when designing a new scenario or executing an existing one to produce a dataset.

1. **Design the timeline:** Include:
   - **Baseline / background noise** so detections are not tested against an empty wire.
   - **Positive controls** — the target behaviors/techniques to be observed.
   - **Negative controls** — deliberate failures, blocked paths, or untouched crown jewels to prove the capture and analytics distinguish allow vs. deny.
2. **Run preflight:** Do not start capture until the preflight gate passes.
3. **Execute with independent ground truth:** Start passive capture, execute the timeline inside real target sessions/hosts (never from the capture point), and log each action to the ground-truth file as it happens.
4. **Collect and package:** Stop capture, export host/directory telemetry, apply any capture deduplication rule, and write the real-vs-synthetic provenance note alongside the dataset.
5. **Reset:** Restore the lab to baseline after collection so the next run starts clean.

**Done when:** Telemetry, ground truth, and real-vs-synthetic provenance are packaged together and the lab is reset.

---

## Workflow 3 — Evaluate Dataset

Use when verifying a newly captured dataset or auditing an existing one.

Check all four criteria, anchoring every finding per `/evidence`:

1. **Positive controls:** Every ground-truth action that should succeed is observed in the captured telemetry (anchored by source, identifier, and UTC timestamp).
2. **Negative controls:** Expected failures, blocks, or untouched targets are confirmed absent or denied in telemetry — with explicit coverage stated before claiming absence.
3. **Control-plane leak check:** Verify no provisioning, orchestration, or driver artifacts appear in the scenario capture (record zero leaks or list exact residual leaks).
4. **Clock alignment:** Measure and record the UTC offset between ground-truth timestamps and captured telemetry.

### Acceptance Verdict

Produce a concise acceptance report with one of three verdicts:

- **`PASS`** — All positive and negative controls verified, zero unrecorded control-plane leaks, clock skew within tolerance, and real-vs-synthetic provenance complete.
- **`PASS WITH GAPS`** — Core controls verified, but known gaps, clock skew, or accepted hardware/topology residuals are explicitly documented and bounded.
- **`FAIL`** — Missing ground truth, unverified positive/negative controls, undocumented control-plane contamination, or broken capture coverage.
