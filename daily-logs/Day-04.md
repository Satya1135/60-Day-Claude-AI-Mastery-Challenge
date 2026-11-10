# Day 04: Raw Evidence Ingestion Pipeline & Integrity Verification

**Date**: 2026-10-07
**Milestone**: Day 4 — Backend & Frontend Evidence Intake Pipeline
**Status**: Completed Successfully
**Source Commit**: `784ab00`
**Reference Document**: `docs/DAY4-EVIDENCE-INTAKE.md` in [ai-linux-investigation-workspace](https://github.com/Satya1135/ai-linux-investigation-workspace)

---

## 1. Objectives & Overview
Day 4 established the evidence ingestion layer for the Linux investigation workstation. The goal was to provide safe intake, parsing, normalization, and cryptographic preservation of raw Linux security logs across multiple intake modes (upload, paste, sample scenario) while guaranteeing that untrusted log lines are treated strictly as inert text data.

---

## 2. Concepts & Topics Studied
- **Cryptographic Provenance & Evidence Fingerprinting**: Using SHA-256 digests computed directly on raw incoming byte streams to establish baseline integrity against post-intake modification.
- **Normalization vs. Preservation**: Balancing the need for byte-for-byte evidence preservation (for legal/chain-of-custody requirements) with line-ending normalization (CRLF to LF) for deterministic downstream parsing.
- **Defensive Ingestion Security**: Mitigating malicious log injection, path traversal attacks, zip bombs, and shell execution hazards when handling attacker-controlled log text.
- **Timestamp Extraction**: Regex parsing across diverse Linux timestamp formats (Syslog BSD, ISO 8601, Auditd epoch format).

---

## 3. Practical Implementation & Features Developed

### A. Intake Specifications & Operational Boundaries
- **Payload Limits**: Enforced a strict 5 MB (`5,242,880` bytes) upload ceiling, returning HTTP 413 `OVERSIZED_EVIDENCE` for oversized payloads.
- **Extension Whitelist**: Restricted uploads strictly to `.log` and `.txt`. Disallowed executable, script, or archive formats (`.exe`, `.sh`, `.zip`, `.elf`).
- **Binary Content Detection**: Scanned for null bytes (`\x00`) and non-UTF-8 sequences, rejecting binary submissions with HTTP 400.
- **Path Traversal Guard**: Enforced path confinement for sample scenario discovery, validating paths strictly stay within `sample-data/linux/`.

### B. Core Backend Services & Schemas (`backend/app/services/evidence/`)
- `PastedEvidenceRequest` and `SampleEvidenceRequest` Pydantic v2 schemas.
- `LogEventCandidate` data model capturing `event_index`, `source_line_number`, `event_time`, and verbatim `raw_message`.
- `extract_safe_timestamp()` supporting Syslog BSD (`Oct 06 08:12:01`), ISO 8601, and Auditd epoch (`audit(1728204270.112:89)`).
- `EvidenceService` handling file streams, computing SHA-256 digests, and generating unique UUID4 identifiers per intake session.

### C. Frontend Evidence Intake UI (`frontend/src/pages/EvidenceIntake.tsx`)
- Three distinct intake modes: Pre-packaged Sample Scenario, Drag-and-Drop / File Upload, and Direct Paste with real-time byte gauge.
- Persistent amber security banner stating: *"Evidence is treated as text only. Commands, scripts, binaries, and files contained in logs are never executed."*
- Raw log viewport rendering lines in monospaced React text primitives inside `<pre>` and `<code>` blocks to prevent DOM-based XSS injection.

### D. Synthetic Multi-Stage Test Scenario
- Created `sample-data/linux/sample-privilege-escalation.log` (30 events) containing SSH brute-forcing, `sudo -l` enumeration, GTFOBins `sudo find` execution, SUID root shell creation, and `/etc/shadow` access.

---

## 4. Challenges & Solutions
- **Challenge**: Log payloads may contain shell commands (e.g., `rm -rf`, `curl | bash`, `$(reboot)`) or malicious HTML/JavaScript tags intended to compromise analyst workstations.
- **Solution**: Completely decoupled ingestion from any shell interpreters, `eval()`, or `exec()`. Enforced pure string manipulation and rendered evidence strictly as plain text in the frontend with zero `dangerouslySetInnerHTML`.

---

## 5. Verification & Testing Results
- **Backend Test Suite**: 31 automated tests passed (unit + integration) covering extension checking, payload limits, empty input rejection, binary detection, and hash determinism.
- **Integration Script**: Verified via `scripts/verify_frontend_api.mjs` with 8/8 successful API flows.
- **Build**: Clean frontend production build (`tsc && vite build`) with zero errors.

---

## 6. Key Takeaways
1. Security logs are untrusted data and must be treated as hostile input: never evaluate, never pass to shell subprocesses, and always render as plain inert text.
2. Generating a deterministic SHA-256 hash immediately upon byte intake creates an immutable reference point that underpins all downstream forensic findings.
