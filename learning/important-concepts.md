# Important Concepts & Architecture Insights

This document captures the key architectural principles, technical concepts, and design decisions mastered during the 60-Day Claude AI Challenge and the capstone sprint.

---

## 1. Deterministic Heuristics vs. Probabilistic LLMs in Forensics

A central breakthrough of the capstone was the deliberate decision **not** to rely on a live Claude API for generating investigation findings.

| Factor | Live LLM API | Deterministic Heuristic Engine |
|:---|:---|:---|
| **Reproducibility** | Non-deterministic; subtle prompt shifts produce varying findings. | 100% reproducible; identical input produces identical output. |
| **Evidence Grounding** | Prone to hallucinations or slight misquotations of line numbers. | Mathematically links exact event IDs and raw line numbers. |
| **Operational Cost** | High token costs on verbose logs (e.g. 50,000-line audit dumps). | Zero API costs; runs locally in pure Python. |
| **Latency** | Network latency + model generation time (seconds to minutes). | Evaluates multi-stage scenarios in milliseconds. |
| **Legal Defensibility** | Difficult to explain internal model weights in a court of law. | Fully transparent, explainable rule conditions and regex patterns. |

**Key Takeaway**: LLMs are powerful for synthesis, drafting, and explaining, but deterministic rules must form the backbone of forensic incident detection.

---

## 2. Evidence Integrity, Hashing & The Limits of SHA-256

- **Post-Intake Traceability**: Computing a SHA-256 hash immediately upon receipt of a raw log file provides a cryptographic fingerprint. If an analyst modifies a file later, the hash discrepancy immediately proves alteration.
- **The Boundary of Ingestion Hashing**: While SHA-256 proves that the file has not changed *since intake*, it does **not** prove external origin authenticity or prevent an attacker from modifying logs *prior* to upload. Establishing true chain-of-custody requires hardware timestamps, signed auditd streams, or append-only remote syslog forwarding.

---

## 3. Untrusted Log Data & The Injection Threat Model

Security logs often contain active attack payloads:
```text
Oct 06 14:22:01 server sudo: attacker : TTY=pts/1 ; COMMAND=/bin/bash -c "rm -rf /; <script>alert(1)</script>"
```
If an investigation platform passes log messages to a shell, evaluates them with `eval()`, or renders them unescaped in an HTML report, the security tool itself becomes an attack vector:
- **Command Injection**: Passing log contents to subprocesses can execute attacker commands.
- **Stored XSS**: Rendering raw logs inside exported HTML reports allows attackers to steal analyst session cookies when reports are reviewed offline.
- **Mitigation Applied**: All log lines are treated as immutable plain text, rendered using React text nodes (never `dangerouslySetInnerHTML`), and strictly sanitized with `escapeHtml()` during HTML report generation.

---

## 4. Browser-Native Print Styles vs. Server-Side PDF Compilers

Generating printable incident reports is a standard enterprise requirement. Many teams default to backend libraries like `WeasyPrint` or headless Chrome instances (`Puppeteer`).

### The Hidden Costs of WeasyPrint:
- Depends on native C-libraries: Cairo, Pango, GDK-PixBuf, and FontConfig.
- Incurred severe installation failures on Windows environments and bloated Linux containers by hundreds of megabytes.

### The Browser-Native Solution:
- Modern CSS provides `@media print`, `@page { size: A4 portrait; margin: 12mm; }`, and `break-inside: avoid;`.
- Calling `window.print()` allows the user's browser to render high-contrast vector PDFs with zero backend dependencies, zero installation friction, and identical cross-platform fidelity.

---

## 5. Client-Side Abstraction for Future Relational Persistence

Implementing client-side `localStorage` on Day 7 was an intentional architectural choice:
- It provided immediate, zero-friction persistence for evaluating the MVP without requiring PostgreSQL connection strings or cloud credentials.
- By designing typed TypeScript interfaces (`SavedInvestigationCase`, `AnalystFindingReview`) that mirror relational database tables, the storage layer can be swapped for a REST/PostgreSQL backend in the future without modifying any UI components.

---

## 6. Port Collision & Environment Synchronization

In full-stack local development, port collisions (e.g. port 8000 already occupied by another service) frequently cause frustrating bugs where the frontend makes requests to a zombie service.
- **Principle**: Never hardcode API hostnames or ports in client source files.
- **Pattern**: Configure a sensible fallback default (`http://localhost:8000`) in code, and override locally via `frontend/.env.local` (`VITE_API_URL=http://localhost:8010`).
- Ensure `.env.local` is strictly ignored by Git so developer machine configurations never leak into production code.
