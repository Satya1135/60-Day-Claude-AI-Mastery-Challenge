# 60-Day Claude AI Mastery Challenge — Completed Archive

**Participant**: Satya1135
**Challenge**: AB Talks 60-Day Claude AI Mastery Challenge
**Status**: ✅ Challenge Completed (Reported by participant)
**Capstone Sprint**: 10 engineering days — fully documented
**Capstone Repository**: [ai-linux-investigation-workspace](https://github.com/Satya1135/ai-linux-investigation-workspace)

---

## About This Archive

This repository is the public archive of my completed AB Talks 60-Day Claude AI Mastery Challenge. It documents the learning journey, technical skills acquired, core learning themes, and links to the main capstone project delivered during the challenge.

**What this archive contains:**
- Challenge overview and learning themes
- Day-by-day engineering logs for the capstone sprint (Days 3–10 with verified commits)
- In-depth documentation on architecture, technical skills, and key concepts
- Reusable prompts, debugging challenges, and retrospective reflections
- Honest progress tracking that distinguishes reported challenge completion from repository-evidenced daily milestones

**What this archive does not contain:**
- Fabricated daily logs, screenshots, certificates, or media files
- Source code for the capstone project (which lives in the dedicated [capstone repository](https://github.com/Satya1135/ai-linux-investigation-workspace))

---

## Main Capstone Project

### AI-Powered Linux Malware & Privilege-Escalation Investigation Workspace

> A full-stack security operations workstation for forensic triage, deterministic threat correlation, analyst case management, and automated incident reporting on Linux systems.

| | |
|:---|:---|
| **Repository** | [github.com/Satya1135/ai-linux-investigation-workspace](https://github.com/Satya1135/ai-linux-investigation-workspace) |
| **Release** | [v1.0.0 — Portfolio Release](https://github.com/Satya1135/ai-linux-investigation-workspace/releases/tag/v1.0.0) |
| **Live Frontend** | [https://ai-linux-investigation-frontend.onrender.com/](https://ai-linux-investigation-frontend.onrender.com/) |
| **Live Backend API** | [https://ai-linux-investigation-workspace.onrender.com/](https://ai-linux-investigation-workspace.onrender.com/) |
| **Classification** | Portfolio / Demo MVP — Local Security Workstation |
| **Sprint** | 10 engineering days (capstone portion of the 60-day challenge) |

> **Architecture Notice**: This project is a **Portfolio/Demo MVP**, not a production-hardened SOC platform. The investigation engine is **deterministic and rule-based** — it does not invoke a live Claude API to generate findings. Claude prompt schemas are scaffolded in Pydantic for future optional narrative assistance only.

For detailed architecture, technical stack, and milestone breakdown, see [projects/project-details.md](projects/project-details.md).

---

## Project Architecture & Technology Stack

### Frontend
| Component | Technology |
|:---|:---|
| Framework | React 18 + TypeScript |
| Bundler | Vite |
| Styling | Vanilla CSS Modules — Cybersecurity HUD design tokens |
| Theme | Deep slate `#090d16` · Cyan `#06b6d4` · Emerald `#10b981` · Rose `#f43f5e` · JetBrains Mono |
| Data Fetching | TanStack Query (React Query) |
| Icons | Lucide React |
| Persistence | Browser `localStorage` for offline case management |

### Backend
| Component | Technology |
|:---|:---|
| Framework | Python 3.11+ / FastAPI |
| Validation | Pydantic v2 / Pydantic Settings |
| Investigation | Deterministic 5-stage heuristic rule engine |
| Testing | Pytest + HTTPX (async) |
| Report Generation | Browser-native `window.print()` + `@media print` CSS |

### Testing & Verification
| Suite | Count | Result |
|:---|:---|:---|
| Backend Pytest | 71 tests | ✅ 100% passed |
| Report Verification (`verify_reports.mjs`) | 13 tests | ✅ 100% passed |
| Frontend Production Build | 0 errors | ✅ build passed |

---

## Major Delivered Features

### 1. Linux Evidence Intake & SHA-256 Fingerprinting
- Upload `auth.log`, `syslog`, and `auditd` files (up to 5 MB).
- SHA-256 fingerprinting on raw log streams for evidence integrity tracking and audit traceability.
- Parser normalization preserving line-by-line forensic provenance.
- Path traversal defense via Python `path.is_relative_to(base_dir)`.

### 2. Forensic Investigation Timeline
- Automated chronological reconstruction across disparate log timestamps.
- Attack stage tagging: `AUTHENTICATION` · `EXECUTION` · `PRIVILEGE_ESCALATION` · `PERSISTENCE`.
- Severity classification: `INFO` · `LOW` · `MEDIUM` · `HIGH` · `CRITICAL`.
- Interactive table with multi-criteria filtering, search, and suspicious event highlighting.

### 3. Deterministic MITRE ATT&CK-Aligned Investigation Engine
5-stage investigation pipeline — `VALIDATION` → `CORRELATION` → `SEQUENCE_ANALYSIS` → `CONFIDENCE_ASSESSMENT` → `RESULT_PREPARATION`.

Detected threat patterns with MITRE ATT&CK alignment:

| Finding | Category | Evidence |
|:---|:---|:---|
| Privilege Discovery → Escalation | `PRIVILEGE_ESCALATION` | `sudo -l` followed by root execution |
| GTFOBins `sudo find` Shell Spawn | `PRIVILEGE_ESCALATION` | `sudo find / -exec /bin/sh` |
| SUID Root Backdoor Shell (`chmod 4755`) | `PERSISTENCE` | `/tmp/.rootshell` creation |
| `/etc/shadow` Credential Access | `POST_EXPLOITATION` | Direct read of shadow file |
| SSH Brute-Force → Successful Login | `AUTHENTICATION_ANOMALY` | Repeated failures then acceptance |
| `/tmp` Payload Staging | `SUSPICIOUS_EXECUTION` | `curl`/`wget` + `chmod +x` |
| Scheduled Task on Staged Binary | `PERSISTENCE` | Crontab entry executing `/tmp` script |

> Detection is deterministic and rule-based. Every finding cites exact original event IDs and log line numbers.

### 4. Analyst Review & Browser-Local Case Management
- Adjudicate findings: `CONFIRMED` · `REJECTED` · `EDITED` · `PENDING`.
- Editable finding explanations and analyst notes.
- Standardized case IDs: `LIN-YYYYMMDD-XXXXX`.
- Browser `localStorage` persistence — save, search, filter, reopen, delete cases offline.
- Immutable separation of original engine output from analyst edits.

### 5. Sanitized HTML & PDF Incident Reports
- 8-section forensic report: executive summary, metrics, timeline, adjudicated findings.
- Standalone HTML export with embedded styles and `escapeHtml()` XSS protection.
- Browser-native PDF via `window.print()` and `@media print` CSS (`break-inside: avoid`).
- Safe filename sanitization preventing directory traversal.

### 6. Responsive UI & Accessibility
- Mobile drawer navigation (< 768 px screen width).
- High-contrast `:focus-visible` keyboard rings.
- ARIA roles, labels, and keyboard activation throughout.
- Horizontal scrolling wrappers for wide data tables.

### 7. Functional Settings Page *(post-sprint)*
- Added on 2026-10-29 in commit `64a7d4b` — after the original 10-day sprint concluded.
- Provides analyst-configurable workspace settings.

---

## Capstone Engineering Milestones

The 10-day capstone sprint covers the documented engineering work. Days 1–2 are unrecorded in the repository (design scoping, threat modelling, tech stack selection prior to the first commit).

| Day | Commit | Date | Milestone |
|:---|:---|:---|:---|
| 1–2 | *(unrecorded)* | Pre-Oct 2026 | Project inception & scoping |
| 3 | `6c464c3` | 2026-10-06 | Foundation: FastAPI + React/TS shell, 4 tests |
| 4 | `784ab00` | 2026-10-07 | Evidence intake, SHA-256, log parsers |
| 5 | `eeb25c0` `792be29` `ca9582a` | 2026-10-08 | Forensic timeline & attack stage tagging |
| 6 | `ea01546` | 2026-10-09 | 5-stage deterministic rule engine + MITRE |
| 7 | `40dc823` | 2026-10-10 | Analyst adjudication + localStorage cases |
| 8 | `a11ff91` `d4debd2` | 2026-10-11 | 8-section reports, HTML & PDF export |
| 9 | `022ae72` | 2026-10-13 | Security hardening, mobile nav, a11y |
| 10 | `cc5d166` | 2026-10-13 | Audit, roadmap, graduation package |
| *(post-sprint)* | `64a7d4b` | **2026-10-29** | Functional Settings page |

---

## Learning Themes

The 60-day challenge covered eight core domains:

| Theme | Focus |
|:---|:---|
| AI Foundations & Prompting | Effective communication with AI, structured prompting |
| Context Engineering | Multi-step task continuity, conversation management |
| AI-Assisted Coding | Pair programming, debugging, iterative improvement |
| Research & Problem Solving | AI-guided research, claim verification, synthesis |
| Automation & Workflow | Repeatable AI workflows, parameterized prompt templates |
| Product Thinking | Scope definition, trade-off analysis, architectural decisions |
| Multimodal & Agentic Tools | Sequential goal-directed AI workflows |
| Knowledge Systems | Persistent documentation, multi-agent collaboration |

See [learning-themes.md](learning-themes.md) and [learning/learning-summary.md](learning/learning-summary.md) for the full breakdown.

---

## Archive Document Index

| Document | Purpose |
|:---|:---|
| [progress-tracker.md](progress-tracker.md) | Day-by-day milestone log and 60-day documentation roadmap |
| [learning-themes.md](learning-themes.md) | Eight core learning themes and capstone applications |
| [learning/learning-summary.md](learning/learning-summary.md) | Comprehensive synthesis of the 60-day learning curriculum |
| [learning/skills-acquired.md](learning/skills-acquired.md) | Catalog of technical and engineering skills acquired |
| [learning/important-concepts.md](learning/important-concepts.md) | Deep dive into architecture principles and forensic concepts |
| [daily-logs/README.md](daily-logs/README.md) | Directory guide for daily engineering logs |
| [daily-logs/Day-03.md](daily-logs/Day-03.md) | Day 3: Project foundation, runtime & health tests |
| [daily-logs/Day-04.md](daily-logs/Day-04.md) | Day 4: Evidence intake pipeline & SHA-256 fingerprinting |
| [daily-logs/Day-05.md](daily-logs/Day-05.md) | Day 5: Forensic timeline reconstruction & attack stages |
| [daily-logs/Day-06.md](daily-logs/Day-06.md) | Day 6: Deterministic heuristic engine & MITRE mapping |
| [daily-logs/Day-07.md](daily-logs/Day-07.md) | Day 7: Analyst case management & localStorage persistence |
| [daily-logs/Day-08.md](daily-logs/Day-08.md) | Day 8: Incident reports, HTML & browser-native PDF export |
| [daily-logs/Day-09.md](daily-logs/Day-09.md) | Day 9: Security hardening, responsive navigation & a11y |
| [daily-logs/Day-10.md](daily-logs/Day-10.md) | Day 10: Integrity audit, roadmap & v1.0.0 release packaging |
| [projects/README.md](projects/README.md) | Projects directory guide |
| [projects/project-details.md](projects/project-details.md) | In-depth technical architecture and capstone project details |
| [prompts/README.md](prompts/README.md) | Prompt library guide and engineering principles |
| [prompts/useful-prompts.md](prompts/useful-prompts.md) | Reusable Daily Build Prompt template and specialized patterns |
| [reflections/challenges-and-solutions.md](reflections/challenges-and-solutions.md) | Real debugging cases and engineering solutions |
| [reflections/final-reflection.md](reflections/final-reflection.md) | Comprehensive capstone reflection and AI mentor farewell |
| [graduation-reflection.md](graduation-reflection.md) | Personal reflection at capstone completion |
| [final-farewell.md](final-farewell.md) | Farewell message from the AI pair programmer |
| [docs/capstone-retrospective.md](docs/capstone-retrospective.md) | Full engineering retrospective — architecture, pivots, test results |

---

## Capstone Documentation (External)

All detailed engineering documentation lives in the [capstone repository](https://github.com/Satya1135/ai-linux-investigation-workspace/tree/main/docs):

- [Day 3 Summary](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY3-SUMMARY.md) — Foundation & local runtime
- [Day 4 Evidence Intake](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY4-EVIDENCE-INTAKE.md) — Log upload & SHA-256
- [Day 5 Timeline](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY5-TIMELINE.md) — Chronological sequencing
- [Day 6 Investigation Pipeline](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY6-INVESTIGATION-PIPELINE.md) — 5-stage engine & MITRE
- [Day 7 Case Management](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY7-CASE-MANAGEMENT.md) — Analyst review & localStorage
- [Day 8 Reports](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/DAY8-REPORTS.md) — HTML & PDF export
- [Challenge Retrospective](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/challenge-retrospective.md) — Full Days 1–10 retrospective
- [Future Scope Roadmap](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/future-scope.md) — 3-, 6-, 12-month plan
- [Portfolio Kit](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/portfolio-kit.md) — Resume bullets, demo script, interview talking points

---

## Challenge Motto

**60 Days. Learn. Build. Experiment. Grow.**

---

*Archive maintained by [Satya1135](https://github.com/Satya1135). Challenge: AB Talks 60-Day Claude AI Mastery Challenge.*
