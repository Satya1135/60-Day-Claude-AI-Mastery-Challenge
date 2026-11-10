# Day 06: Deterministic Investigation Pipeline & MITRE ATT&CK Mapping

**Date**: 2026-10-09
**Milestone**: Day 6 — 5-Stage Heuristic Engine & Investigation Analysis UI
**Status**: Completed Successfully
**Source Commit**: `ea01546`
**Reference Document**: `docs/DAY6-INVESTIGATION-PIPELINE.md` in [ai-linux-investigation-workspace](https://github.com/Satya1135/ai-linux-investigation-workspace)

---

## 1. Objectives & Overview
Day 6 delivered the core automated threat correlation capability: a 5-stage deterministic investigation pipeline that evaluates chronological event sequences, identifies complex Linux attack patterns, scores detection confidence, maps findings to MITRE ATT&CK techniques, and presents an interactive investigation analysis interface.

---

## 2. Concepts & Topics Studied
- **Deterministic Multi-Event Correlation**: Correlating multiple independent log events across time (e.g., matching a sequence of 5 failed SSH attempts followed by 1 successful login from the same IP within a temporal window).
- **MITRE ATT&CK Framework Mapping**: Standardizing Linux adversary behaviors against recognized Enterprise tactics and techniques:
  - T1078 (Valid Accounts / Brute Force)
  - T1059.004 (Unix Shell Execution)
  - T1548.003 (Sudo and Sudo Caching / SUID abuse)
  - T1003.008 (/etc/passwd and /etc/shadow credential dumping)
  - T1053.003 (Cron persistence)
- **Deterministic Confidence Scoring**: Formulating repeatable mathematical confidence percentages based on event corroboration and pattern specificity, rather than non-deterministic LLM probability.
- **Architectural Pivot (Heuristics vs. Live API)**: Choosing deterministic rule evaluation over live Claude API calls to ensure zero token costs, sub-second latency, zero hallucinations, and 100% reproducible forensic citations.

---

## 3. Practical Implementation & Features Developed

### A. 5-Stage Investigation Pipeline (`backend/app/services/investigation/pipeline.py`)
1. **Validation Stage**: Validates event candidates and timeline structure against Pydantic schemas.
2. **Correlation Stage**: Groups events by entity, user session, process, and IP address.
3. **Sequence Analysis Stage**: Detects multi-event attack chains and temporal progressions.
4. **Confidence Assessment Stage**: Computes mathematical confidence scores (0.0 to 1.0) based on corroborating artifacts and pattern fidelity.
5. **Result Preparation Stage**: Compiles findings with MITRE tags, recommended analyst actions, and supporting event IDs.

### B. Seven Grounded Detections (Sample Scenario)
The pipeline extracts seven distinct findings with 100% evidence grounding from the 30-event scenario:
1. **Privilege Discovery Leading to Privilege Escalation** (`CRITICAL`, `PRIVILEGE_ESCALATION`)
2. **Privilege Escalation via Sudo Find Shell Spawn (GTFOBins)** (`CRITICAL`, `PRIVILEGE_ESCALATION`)
3. **Creation of SUID Root Backdoor Shell** (`CRITICAL`, `PERSISTENCE`)
4. **Sensitive Credential Store Access (`/etc/shadow`)** (`CRITICAL`, `POST_EXPLOITATION`)
5. **SSH Brute-Force Password Guessing Followed by Success** (`HIGH`, `AUTHENTICATION_ANOMALY`)
6. **Remote Payload Staging and Execution Permissions in `/tmp`** (`HIGH`, `SUSPICIOUS_EXECUTION`)
7. **Persistent Scheduled Task Executing Staged Binary** (`HIGH`, `PERSISTENCE`)

### C. Investigation Analysis UI (`frontend/src/pages/InvestigationAnalysis.tsx`)
- High-level verdict banner (`SUSPICIOUS_ACTIVITY_DETECTED` / `CLEAN`).
- Executive summary card and overall threat metrics.
- Interactive Finding Cards displaying severity badge, confidence meter, MITRE ATT&CK tags, detailed technical explanation, and clickable supporting event badges.

---

## 4. Challenges & Solutions
- **Challenge**: The temptation to use live Claude API calls for log analysis risks token exhaustion on large log files (e.g. 50,000 lines), latency spikes, and potential analytical hallucinations.
- **Solution**: Built the core engine on deterministic heuristics in Python. Scaffolding schemas for Claude were preserved for optional future narrative drafting, keeping the core detection pipeline 100% free, local, fast, and reproducible.

---

## 5. Verification & Testing Results
- **Backend Tests**: 58/58 tests passing (including comprehensive unit tests for GTFOBins, brute-force clustering, and confidence math).
- **Integration Verification**: Verified through `scripts/verify_frontend_api.mjs` confirming accurate end-to-end finding extraction and event index tracking.
- **Frontend Build**: Verified clean TypeScript compilation and Vite bundling with zero errors.

---

## 6. Key Takeaways
1. In forensic cybersecurity, deterministic rules are superior to probabilistic LLM completions for finding generation because every finding must cite verifiable, immutable log lines.
2. Mapping findings to MITRE ATT&CK techniques provides instant context for SOC analysts to formulate containment and remediation strategies.
