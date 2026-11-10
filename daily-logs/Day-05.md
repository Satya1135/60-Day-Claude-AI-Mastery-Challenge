# Day 05: Forensic Timeline Reconstruction & Cyber Defense Theme

**Date**: 2026-10-08
**Milestone**: Day 5 — Chronological Sequencing, Attack Stage Tagging & Forensic UI
**Status**: Completed Successfully
**Source Commits**: `eeb25c0`, `792be29`, `ca9582a`
**Reference Document**: `docs/DAY5-TIMELINE.md` in [ai-linux-investigation-workspace](https://github.com/Satya1135/ai-linux-investigation-workspace)

---

## 1. Objectives & Overview
Day 5 focused on transforming normalized candidate log events into an interactive, chronologically ordered forensic investigation timeline. The system maps events to MITRE-aligned attack stages, tags security anomalies with mathematical severity, provides bidirectional provenance back to raw evidence line numbers, and presents an analyst-grade three-column cockpit UI.

---

## 2. Concepts & Topics Studied
- **Forensic Event Sequencing**: Handling disparate timestamp formats across heterogeneous Linux log sources and sorting them into a unified chronological progression.
- **MITRE-Aligned Attack Progression**: Categorizing log entries into sequential incident response stages: `AUTHENTICATION`, `RECONNAISSANCE` / `PRIVILEGE_DISCOVERY`, `PRIVILEGE_ESCALATION`, `EXECUTION`, `PERSISTENCE`, and `POST_EXPLOITATION`.
- **Deterministic Anomaly Tagging**: Implementing rule-based severity scoring (`INFO`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`) with human-readable rationale without external AI dependencies.
- **Forensic Traceability Chains**: Establishing unbroken links from high-level timeline events (`evt-00X`) to normalized candidate records (`#X`) to original source log line numbers.

---

## 3. Practical Implementation & Features Developed

### A. Backend Timeline Service (`backend/app/services/timeline/service.py`)
- `TimelineService.reconstruct_timeline()`: Ingests `LogEventCandidate` objects, applies temporal sorting, and assigns unique timeline identifiers.
- Heuristic anomaly tagging identifying:
  - Repeated failed SSH logins followed by successful authentication.
  - Execution of reconnaissance utilities (`sudo -l`, `whoami`, `id`).
  - SUID shell spawning (`chmod 4755`) and root privilege transitions (`euid=0`).
  - Execution of GTFOBins patterns (`/usr/bin/find` with `-exec`).
  - Sensitive file access to `/etc/shadow`.
- Explicit transparency flag: Every response includes `"classification_method": "rule_based"`, informing analysts that findings originate from deterministic rules.

### B. Frontend Forensic Cockpit (`frontend/src/pages/InvestigationTimeline.tsx`)
- **Three-Column Layout**:
  - **Left Rail**: Attack Stage Rail displaying detected stage event counts and quick filter toggles.
  - **Center Stream**: Chronological event feed with interactive timeline nodes, severity tags, process indicators, and search filter.
  - **Right Pane**: Selected event detail pane with forensic property grid, highlight alert box, evidence breadcrumbs, and raw source log viewport.
- **Multi-Field Client Search**: Instant search across raw log messages, processes, hosts, event types, and event IDs with zero repeated API roundtrips.
- **Interactive Multi-Filter Combinations**: Seamlessly combine Attack Stage + Suspicious Only + Keyword Search with interactive removable filter chips and a 1-click `RESET ALL FILTERS` action.
- **Resilient Selection Synchronization**: Automatically synchronizes the detail view to the first visible event when previous selections are filtered out, avoiding stale displays.

---

## 4. Challenges & Solutions
- **Challenge**: Multiple disparate timestamp formats in a single log dump (e.g. BSD syslog without year vs. ISO 8601 with timezone offsets) can lead to sorting errors.
- **Solution**: Built an automated timestamp normalizer that standardizes extracted timestamps into comparable chronological strings while retaining the literal verbatim time string for analyst review.

---

## 5. Verification & Testing Results
- **Backend Pytest Suite**: 45/45 tests passed (31 Day 4 tests + 14 Day 5 unit and integration tests) in 0.57s.
- **Sample Scenario Evaluation**: Evaluated `sample-privilege-escalation.log`:
  - 30 total events processed.
  - 17 suspicious events detected and highlighted.
  - Detected stages: `AUTHENTICATION`, `PRIVILEGE_DISCOVERY`, `PRIVILEGE_ESCALATION`, `EXECUTION`, `PERSISTENCE`, `POST_EXPLOITATION`, `OTHER`.
- **Frontend Build**: `npm run build` completed with 0 errors in 8.72s.

---

## 6. Key Takeaways
1. Security analysts need multi-faceted filtering: isolating attack stages or suspicious anomalies instantly reduces cognitive overload during incident triage.
2. Unbroken evidence traceability—from timeline event back to the exact source line number—is essential for report defensibility in forensics.
