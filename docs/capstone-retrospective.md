# Capstone Retrospective — AI-Powered Linux Investigation Workspace

> This is an archive copy of the full engineering retrospective. The authoritative source lives in the capstone repository:  
> [docs/challenge-retrospective.md](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/challenge-retrospective.md)

---

## Project at a Glance

**Project Title**: AI-Powered Linux Malware & Privilege-Escalation Investigation Workspace  
**Repository**: [github.com/Satya1135/ai-linux-investigation-workspace](https://github.com/Satya1135/ai-linux-investigation-workspace)  
**Release**: [v1.0.0](https://github.com/Satya1135/ai-linux-investigation-workspace/releases/tag/v1.0.0)  
**Classification**: Portfolio / Demo MVP — Local Security Workstation  
**Sprint Duration**: 10 engineering days  
**Retrospective Date**: October 2026  
**Latest Sprint Commit**: `022ae72` — `feat: complete Day 9 security and responsive UX improvements`  
**Post-Sprint Enhancement**: `64a7d4b` — `feat: add functional investigation settings` (2026-10-29, after the original sprint)

---

## 1. What Was Built

A full-stack security operations workstation that enables security analysts to:

1. **Ingest** untrusted Linux system logs (`auth.log`, `syslog`, `auditd`) with SHA-256 evidence fingerprinting
2. **Reconstruct** chronological forensic timelines with automatic attack stage tagging
3. **Correlate** events deterministically through a 5-stage rule-based investigation engine
4. **Adjudicate** findings (Confirm, Reject, Edit, Pend) with analyst notes
5. **Manage** investigation cases in browser-local storage (`LIN-YYYYMMDD-XXXXX` format)
6. **Export** formal 8-section incident reports as sanitized HTML or browser-native PDF

> **Important**: The active investigation engine is **deterministic and rule-based**. It does not invoke a live Claude API or any LLM to generate findings. Claude prompt schemas are scaffolded in Pydantic for future optional narrative assistance only.

---

## 2. Technology Stack

### Frontend
| Component | Technology |
|:---|:---|
| Framework | React 18 + TypeScript |
| Bundler | Vite |
| Styling | Vanilla CSS Modules, custom design tokens |
| Data Fetching | TanStack Query (React Query) |
| Icons | Lucide React |
| Persistence | Browser `localStorage` |

### Backend
| Component | Technology |
|:---|:---|
| Framework | Python 3.11+ / FastAPI |
| Validation | Pydantic v2 / Pydantic Settings |
| Investigation | Deterministic heuristic rule engine |
| Testing | Pytest, HTTPX |
| Reporting | Browser-native `window.print()` + `@media print` CSS |

---

## 3. Day-by-Day Engineering Milestones

```
Day 1–2 [Unrecorded] ──► Day 3 [Foundation & Shell] ──► Day 4 [Evidence Intake]
                                      │
Day 7 [Case Management] ◄── Day 6 [Rule Engine] ◄── Day 5 [Timeline Sequencing]
         │
Day 8 [Reports & PDF] ──► Day 9 [Security & UX Polish] ──► Day 10 [Audit & Roadmap]

(Settings enhancement: 2026-10-29, commit 64a7d4b — after the sprint)
```

### Days 1 & 2 — Project Inception (Historical Gap)
No dedicated milestone documents exist for Days 1–2. Git history begins at commit `6c464c3` on Day 3.  
*Reconstructed context*: Initial design scoping, threat modeling for Linux attack vectors (GTFOBins, SUID abuse, SSH brute-forcing), and tech stack selection.

### Day 3 — Project Foundation & Local Runtime
**Commit**: `6c464c3` | **Date**: 2026-10-06  
**Source**: [docs/DAY3-SUMMARY.md](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY3-SUMMARY.md)

- FastAPI backend with Pydantic v2 settings and CORS middleware
- Health check endpoints verified with **4 automated unit tests**
- Vite + React 18 + TypeScript frontend shell with TanStack Query
- Cybersecurity HUD design system (deep slate `#090d16`, cyan `#06b6d4`, emerald `#10b981`, rose `#f43f5e`, JetBrains Mono)
- Strict secret isolation with `.env.example` and `.gitignore` guardrails

### Day 4 — Raw Evidence Ingestion Pipeline
**Commit**: `784ab00`  
**Source**: [docs/DAY4-EVIDENCE-INTAKE.md](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY4-EVIDENCE-INTAKE.md)

- Evidence upload and ingestion service (`/api/v1/evidence/upload`, `/api/v1/evidence/sample`)
- **SHA-256 fingerprinting** on raw log streams for evidence integrity tracking
- Parser normalization for `auth.log`, `syslog`, and `auditd` formats
- Line-by-line index preservation for forensic provenance

### Day 5 — Forensic Timeline Reconstruction
**Commits**: `eeb25c0`, `792be29`, `ca9582a`  
**Source**: [docs/DAY5-TIMELINE.md](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY5-TIMELINE.md)

- Automated chronological ordering across disparate log timestamps
- Heuristic attack stage tagging: `AUTHENTICATION`, `EXECUTION`, `PRIVILEGE_ESCALATION`, `PERSISTENCE`
- Event severity classification: `INFO`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`
- Interactive forensic timeline table with filtering, search, and suspicious event badges

### Day 6 — Deterministic Investigation Pipeline & MITRE Mapping
**Commit**: `ea01546`  
**Source**: [docs/DAY6-INVESTIGATION-PIPELINE.md](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY6-INVESTIGATION-PIPELINE.md)

5-stage investigation pipeline:
1. `VALIDATION` → 2. `CORRELATION` → 3. `SEQUENCE_ANALYSIS` → 4. `CONFIDENCE_ASSESSMENT` → 5. `RESULT_PREPARATION`

Detected threat patterns with MITRE ATT&CK mapping:
- SUID shell creation (`chmod 4755`)
- GTFOBins `/usr/bin/find` execution
- `/etc/shadow` credential access
- SSH brute-force clustering
- `/tmp` staging detection

### Day 7 — Analyst Case Management & Browser Persistence
**Commit**: `40dc823`  
**Source**: [docs/DAY7-CASE-MANAGEMENT.md](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY7-CASE-MANAGEMENT.md)

- Finding adjudication: `CONFIRMED`, `REJECTED`, `EDITED`, `PENDING`
- Case IDs in `LIN-YYYYMMDD-XXXXX` format
- Browser `localStorage` persistence — save, update, search, filter, reopen, delete
- Immutable separation of original engine output from analyst edits

### Day 8 — Formal Incident Reporting & Browser-Native PDF
**Commits**: `a11ff91`, `d4debd2`  
**Source**: [docs/DAY8-REPORTS.md](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY8-REPORTS.md)

- 8-section forensic report (executive summary, metrics, findings, timeline)
- Standalone HTML export with embedded styles and `escapeHtml()` XSS protection
- Browser-native PDF via `window.print()` and `@media print` CSS (`break-inside: avoid`)
- Safe filename sanitization preventing directory traversal

### Day 9 — Security Hardening, Responsive Navigation & Accessibility
**Commit**: `022ae72`

- Path traversal defense using Python `path.is_relative_to(base_dir)`
- CORS configuration with production Render URL
- Dashboard metrics synchronized from live `localStorage` case count
- Responsive mobile drawer navigation (< 768px screen width)
- Accessibility: `:focus-visible` rings, ARIA roles/labels, keyboard activation

### Day 10 — Integrity Audit & Long-Term Roadmap
**Commit**: `cc5d166` (2026-10-13)

- Local repository verification against remote tracking
- Long-term documentation suite: `future-scope.md`, `30-day-growth-plan.md`, `portfolio-kit.md`

### Post-Sprint — Functional Settings Page
**Commit**: `64a7d4b` (2026-10-29)

> **Note**: This enhancement was completed **after** the original 10-day sprint concluded, on 2026-10-29. It is not part of the Day 10 work.

---

## 4. The Seven Grounded Findings (Sample Scenario)

The 30-event sample scenario (`sample-privilege-escalation.log`) produces seven deterministic findings with 100% evidence grounding:

| # | Severity | Finding | Attack Category |
|:-:|:---|:---|:---|
| 1 | `CRITICAL` | Privilege Discovery Leading to Escalation | `PRIVILEGE_ESCALATION` |
| 2 | `CRITICAL` | Privilege Escalation via Sudo Find Shell Spawn (GTFOBins) | `PRIVILEGE_ESCALATION` |
| 3 | `CRITICAL` | Creation of SUID Root Backdoor Shell | `PERSISTENCE` |
| 4 | `CRITICAL` | Sensitive Credential Store Access (`/etc/shadow`) | `POST_EXPLOITATION` |
| 5 | `HIGH` | SSH Brute-Force Followed by Successful Login | `AUTHENTICATION_ANOMALY` |
| 6 | `HIGH` | Remote Payload Staging and Execution in `/tmp` | `SUSPICIOUS_EXECUTION` |
| 7 | `HIGH` | Persistent Scheduled Task Executing Staged Binary | `PERSISTENCE` |

Every finding cites exact original event IDs and log line indices.

---

## 5. Verified Test Results

```
┌────────────────────────────────────────────────────────┐
│            VERIFIED AUTOMATED TEST RESULTS             │
├──────────────────────────────┬──────────┬──────────────┤
│ Test Suite                   │ Count    │ Pass Rate    │
├──────────────────────────────┼──────────┼──────────────┤
│ Backend Pytest Suite         │ 71 Tests │ 100% Passed  │
│ Standalone Report Suite      │ 13 Tests │ 100% Passed  │
│ Frontend Production Build    │ 0 Errors │ Build Passed │
└──────────────────────────────┴──────────┴──────────────┘
```

---

## 6. Major Architectural Pivots

### Pivot 1: Deterministic Engine vs. Live LLM API
**Decision**: Build a deterministic, rule-based forensic correlation engine instead of relying on live Claude API calls.  
**Rationale**: LLM APIs introduce hallucinations, token costs, latency, and external dependencies.  
**Result**: Zero API costs, millisecond analysis, 100% reproducible results, verifiable evidence provenance.

### Pivot 2: Browser-Native PDF vs. WeasyPrint
**Decision**: Replace WeasyPrint with `window.print()` and `@media print` CSS.  
**Rationale**: WeasyPrint requires heavy native C-libraries (Cairo, Pango, GDK-PixBuf) that crash on Windows without custom GTK installers.  
**Result**: Zero binary dependencies, identical rendering across all modern browsers.

### Pivot 3: Browser localStorage vs. Premature Cloud Database
**Decision**: Implement client-side `localStorage` persistence for MVP instead of Supabase/PostgreSQL.  
**Rationale**: Cloud databases require credentials, migrations, and network connectivity that complicate local evaluation.  
**Result**: Complete offline functionality, zero setup barrier, clean migration path for future SQL persistence.

---

## 7. Known Limitations (MVP)

This project is a **Portfolio / Demo MVP**, not a production-hardened SOC platform:

1. **Unauthenticated API**: Backend accepts requests without authentication tokens
2. **No Rate Limiting**: Upload and analysis endpoints have no throttling
3. **Browser-Only Storage**: Cases disappear if browser data is cleared
4. **Deterministic Heuristics Only**: No live LLM analysis; rules can produce false positives or false negatives
5. **Single-Analyst Workstation**: Not designed for multi-tenant enterprise deployment

See the [Future Scope Roadmap](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/future-scope.md) for the 3-, 6-, and 12-month plan to address these boundaries.

---

## 8. Deployment Information

| Environment | URL |
|:---|:---|
| Live Frontend | [https://ai-linux-investigation-frontend.onrender.com/](https://ai-linux-investigation-frontend.onrender.com/) |
| Live Backend API | [https://ai-linux-investigation-workspace.onrender.com/](https://ai-linux-investigation-workspace.onrender.com/) |
| Local Frontend (dev) | `http://localhost:5173/` |
| Local Backend (dev) | `http://127.0.0.1:8010/` |

---

*Full retrospective source: [docs/challenge-retrospective.md](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/challenge-retrospective.md) in the capstone repository.*
