# Technical Skills Acquired — 60-Day Claude AI Mastery Challenge

This document catalogs the concrete, verifiable engineering and AI skills acquired and demonstrated throughout the challenge and capstone sprint.

---

## 1. AI-Assisted Engineering & Prompt Crafting

- **Parameterized Prompt Engineering**: Designing structured prompt templates (e.g., `docs/daily-build-prompt.md`) with explicit constraints, technology versions, file boundaries, and acceptance criteria.
- **Context Optimization**: Providing targeted file excerpts, stack traces, and interface contracts to AI models to maximize reasoning quality within context limits.
- **Architectural Dialogue**: Using AI pair programming to evaluate trade-offs (e.g., deterministic heuristics vs. probabilistic LLM APIs, server PDF generation vs. client print stylesheets).
- **Automated Test Scaffolding**: Directing AI to generate comprehensive test matrices covering happy paths, edge cases, error conditions, and adversarial inputs.

---

## 2. Backend Engineering (Python & FastAPI)

- **FastAPI REST API Design**: Building schema-enforced, asynchronous RESTful endpoints with Pydantic v2 validation.
- **Secret & Environment Isolation**: Using `pydantic-settings.BaseSettings` for robust configuration loading from `.env` files without hardcoded secrets.
- **Defensive File Ingestion**:
  - Processing streaming uploads up to 5 MB with strict extension enforcement (`.log`, `.txt`).
  - Implementing binary byte detection (null bytes `\x00` and non-UTF-8 rejection).
  - Path traversal defenses using Python 3's `Path.is_relative_to(base_dir)`.
- **Forensic Evidence Processing**:
  - Computing cryptographic SHA-256 digests on raw byte streams for tamper-evident provenance.
  - Parsing heterogeneous Linux timestamp formats (Syslog BSD, ISO 8601, Auditd epoch).
  - Preserving raw line-by-line index relationships between original logs and normalized event candidates.
- **Deterministic Threat Detection**:
  - Developing a 5-stage heuristic correlation pipeline in pure Python.
  - Detecting Linux privilege-escalation patterns: GTFOBins `/usr/bin/find` execution, SUID shell creation (`chmod 4755`), `/etc/shadow` credential theft, SSH brute-forcing, and `/tmp` binary staging.
  - Calculating mathematical confidence scores based on corroborating artifacts and event proximity.
- **Automated Testing**:
  - Writing 71 automated Pytest unit and integration tests using `TestClient` and `httpx.AsyncClient`.
  - Managing test configurations via `pytest.ini` with `asyncio_mode = auto`.

---

## 3. Frontend Engineering (React, TypeScript & Vite)

- **Modern TypeScript & React 18**:
  - Strict type checking (`tsc --noEmit`) with shared interface definitions across entities.
  - Component architecture utilizing functional components and custom React hooks.
- **Fast Build Tooling**: Leveraging Vite for sub-second development server HMR and production bundling.
- **Asynchronous State & Data Fetching**: Implementing `@tanstack/react-query` (TanStack Query) for declarative server-state management, automated background polling, and cache invalidation.
- **Design Systems & HUD Styling**:
  - Authoring modular CSS Modules backed by custom CSS variable design tokens.
  - Styling a cybersecurity SOC HUD palette (deep slate `#090d16`, cyan `#06b6d4`, emerald `#10b981`, rose `#f43f5e`, JetBrains Mono).
- **Client-Side Persistence**:
  - Abstracting browser `localStorage` into a typed case management service (`caseStorage.ts`).
  - Handling CRUD operations, multi-criteria filtering, and case ID generation (`LIN-YYYYMMDD-XXXXX`).
- **Responsive Navigation & Accessibility (a11y)**:
  - Designing a mobile drawer navigation pattern for viewports under 768px.
  - Enforcing high-contrast `:focus-visible` outline rings for keyboard navigation.
  - Applying semantic ARIA roles, labels, and table wrappers.

---

## 4. Cybersecurity & Digital Forensics

- **Linux Threat Analysis**:
  - Understanding Linux attack chains: initial reconnaissance, SSH brute force, privilege escalation, SUID abuse, credential dumping, and cron persistence.
  - Mapping threats to MITRE ATT&CK techniques (T1078, T1059.004, T1548.003, T1003.008, T1053.003).
- **Forensic Integrity & Traceability**:
  - Ensuring unbroken provenance chains from investigation findings to timeline events to raw source log lines.
  - Treating untrusted log evidence strictly as inert text to prevent log injection and code execution.
- **Defensive Output Generation**:
  - Implementing HTML entity sanitization (`escapeHtml()`) to prevent stored cross-site scripting (XSS) in exported reports.
  - Sanitizing file download names against directory traversal sequences.

---

## 5. DevOps, Architecture & Systems Debugging

- **Cross-Origin Configuration**: Managing CORS origins across development ports (`localhost:5173`, `127.0.0.1:5173`) and cloud hosting (Render).
- **Port Conflict Isolation**: Debugging and configuring alternate development ports (`8010` backend vs. `8000`) and isolating settings via `frontend/.env.local`.
- **Browser-Native Printing Architecture**: Replacing fragile server-side C-library dependencies (WeasyPrint/Cairo) with standard `@media print` CSS and `window.print()` for reliable, zero-dependency A4 PDF rendering.
- **Automated Verification Scripting**: Writing standalone Node.js verification scripts (`verify_reports.mjs`) to test reporting logic, sanitization, and backward compatibility.
