# Capstone Project Details: AI-Powered Linux Malware & Privilege-Escalation Investigation Workspace

**Repository**: [https://github.com/Satya1135/ai-linux-investigation-workspace](https://github.com/Satya1135/ai-linux-investigation-workspace)
**Release**: [v1.0.0 — Portfolio Release](https://github.com/Satya1135/ai-linux-investigation-workspace/releases/tag/v1.0.0)
**Classification**: Developer Portfolio / Security Workstation MVP (v1.0.0)
**Sprint Duration**: 10 Engineering Days (October 2026)
**Subsequent Enhancements**: Functional Settings page added on October 29, 2026 (commit `64a7d4b`)

---

## 1. Executive Summary

The **AI-Powered Linux Malware & Privilege-Escalation Investigation Workspace** is an open-source, full-stack cybersecurity workstation designed for rapid forensic triage, evidence parsing, privilege-escalation detection, analyst adjudication, and automated incident reporting on Linux systems.

It allows security analysts to ingest untrusted Linux logs (`auth.log`, `syslog`, `auditd`), reconstruct unified chronological forensic timelines, execute deterministic rule-based threat correlation, adjudicate findings with full auditability, and generate formal incident reports in both standalone HTML and print-ready PDF formats.

> **Critical Architecture Notice**: Active threat detection is **100% deterministic and rule-based**. The application does **not** invoke a live Claude API or any LLM to generate findings during triage. Pydantic prompt schemas for Claude are scaffolded for future optional narrative assistance.

---

## 2. Technology Stack

### Frontend
- **Framework**: React 18 with TypeScript (strict type checking)
- **Tooling & Bundler**: Vite (fast HMR and optimized production bundling)
- **Styling**: Vanilla CSS Modules utilizing custom CSS variable design tokens (Cybersecurity HUD theme: deep slate `#090d16`, cyan `#06b6d4`, emerald `#10b981`, rose `#f43f5e`, JetBrains Mono typography)
- **State Management & Data Fetching**: TanStack Query (`@tanstack/react-query`) with automatic background health polling
- **Icons**: Lucide React
- **Persistence**: Browser `localStorage` for offline, client-side case management

### Backend
- **Framework**: Python 3.11+ / FastAPI
- **Data Validation & Settings**: Pydantic v2 / `pydantic-settings`
- **Detection Engine**: 5-stage deterministic heuristic correlation engine in Python
- **Testing**: Pytest with `httpx` async test client
- **Report Generation**: Browser-native `window.print()` with `@media print` CSS and standalone HTML export (`escapeHtml` protection)

---

## 3. Architecture & Data Flow

```
Raw Linux Logs (auth.log, syslog, auditd) OR Sample Scenario
                     │
                     ▼
  [ Backend Evidence Service ] ──► Computes SHA-256 Fingerprint
                     │             Normalizes CRLF to LF
                     │             Enforces 5 MB limit & non-binary UTF-8
                     ▼
  [ Backend Timeline Service ] ──► Temporal sorting & attack stage tagging
                     │             (AUTHENTICATION, EXECUTION, PRIVILEGE_ESCALATION, etc.)
                     ▼
  [ Deterministic Rule Engine ] ──► 5-Stage Heuristic Correlation:
                     │             SUID root shells, GTFOBins sudo find,
                     │             /etc/shadow access, SSH brute-forcing
                     ▼
  [ React HUD Frontend ] ───────► Interactive Timeline & Finding Cards
                     │             Analyst Adjudication (Confirm, Reject, Edit, Pend)
                     ▼
  [ Browser LocalStorage ] ─────► Persists cases under LIN-YYYYMMDD-XXXXX
                     │
                     ▼
  [ Incident Reporting ] ───────► Standalone Sanitized HTML & Browser-Native PDF
```

---

## 4. Key Implemented Capabilities

### A. Evidence Intake & Verification
- Supports plain-text Linux log files up to 5 MB.
- Computes cryptographic SHA-256 digests immediately on incoming byte streams.
- Rejects binary files (null bytes) and non-whitelisted file extensions.
- Path traversal defense ensures sample loaders cannot escape the designated data directory (`Path.is_relative_to`).

### B. Forensic Timeline Reconstruction
- Automated chronological sequencing across disparate log timestamp formats.
- Attack stage classification: `AUTHENTICATION`, `PRIVILEGE_DISCOVERY`, `PRIVILEGE_ESCALATION`, `EXECUTION`, `PERSISTENCE`, `POST_EXPLOITATION`, `OTHER`.
- Severity classification: `INFO`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`.
- Interactive multi-criteria filtering combining search, stage isolation, and suspicious-only toggles.

### C. Deterministic Investigation Pipeline
A 5-stage heuristic correlation pipeline evaluates events against MITRE ATT&CK techniques:
1. **Validation**: Schema integrity verification.
2. **Correlation**: Entity and session clustering.
3. **Sequence Analysis**: Multi-event attack pattern identification.
4. **Confidence Assessment**: Repeatable mathematical scoring (0.0 to 1.0).
5. **Result Preparation**: Linking findings to exact original event IDs and log line indices.

### D. The Seven Grounded Detections (Sample Scenario)
Evaluated against `sample-privilege-escalation.log` (30 events):
1. **Privilege Discovery Leading to Privilege Escalation** (`CRITICAL`, `PRIVILEGE_ESCALATION`)
2. **Privilege Escalation via Sudo Find Shell Spawn (GTFOBins)** (`CRITICAL`, `PRIVILEGE_ESCALATION`)
3. **Creation of SUID Root Backdoor Shell** (`CRITICAL`, `PERSISTENCE`)
4. **Sensitive Credential Store Access (`/etc/shadow`)** (`CRITICAL`, `POST_EXPLOITATION`)
5. **SSH Brute-Force Password Guessing Followed by Success** (`HIGH`, `AUTHENTICATION_ANOMALY`)
6. **Remote Payload Staging and Execution Permissions in `/tmp`** (`HIGH`, `SUSPICIOUS_EXECUTION`)
7. **Persistent Scheduled Task Executing Staged Binary** (`HIGH`, `PERSISTENCE`)

### E. Analyst Review & Case Management
- Full adjudication workflow: mark findings as `CONFIRMED`, `REJECTED`, `EDITED`, or `PENDING`.
- Finding-level rationale notes and case-wide investigative directives.
- Client-side persistence in `localStorage` under standardized IDs: `LIN-YYYYMMDD-XXXXX`.
- Immutable preservation of original engine findings alongside analyst revisions.

### F. Incident Reporting & Export
- Comprehensive 8-section forensic report viewer.
- Standalone sanitized HTML export with embedded styles and XSS entity escaping (`escapeHtml()`).
- Browser-native PDF rendering via `window.print()` with `@media print` CSS, high-contrast print styling, and `break-inside: avoid;` pagination controls.

### G. Functional Settings Page (Post-Sprint Enhancement)
- Added on **October 29, 2026** in commit `64a7d4b`.
- Allows analysts to configure local application preferences and environment options.
- Documented clearly as a post-sprint enhancement separate from the initial 10-day capstone sprint.

---

## 5. Automated Verification Results

At the completion of the Day 10 audit, the workspace achieved:
- **Backend Pytest Suite**: 71 tests passing (100% pass rate).
- **Report Verification Suite**: 13 automated checks passing (`scripts/verify_reports.mjs`).
- **Frontend Production Build**: `npm run build` compiled with 0 TypeScript errors and 0 Vite warnings.

---

## 6. Security Notice & Current Limitations (MVP Classification)

This software is released as a **Portfolio / Demo MVP (v1.0.0)**. It is intended for local forensic investigation and demonstration. It has the following documented boundaries:
1. **Unauthenticated API Endpoints**: Endpoints currently do not require bearer tokens; security relies on localhost binding and CORS origin validation.
2. **No Rate Limiting**: The backend does not throttle rapid ingestion or analysis requests.
3. **Browser-Local Storage**: Cases are stored client-side in the browser profile; clearing browser data clears saved cases.
4. **Deterministic Heuristics Baseline**: Rules are tuned to specific patterns and may produce false positives or false negatives on unseen telemetry.

See [`docs/future-scope.md`](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/future-scope.md) in the capstone repository for the 3-, 6-, and 12-month roadmap addressing these boundaries.
