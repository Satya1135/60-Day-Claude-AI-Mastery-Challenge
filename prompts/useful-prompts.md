# Useful Prompts & Engineering Templates

This document records the prompt engineering patterns, reusable templates, and structured workflows developed and refined during the 60-Day Claude AI Mastery Challenge and the capstone sprint.

---

## 1. The Daily Build Prompt Template

The primary prompt engineering artifact developed during the capstone is the **Reusable Daily Build Prompt**. It enforces strict safety guardrails, pre-build repository audits, incremental code modifications, automated test execution, and explicit review stop conditions.

### Full Prompt Template

```markdown
Role: Senior Software Engineer, Cybersecurity Architect, Product Mentor, and AI Pair Programmer.

Project: AI-Powered Linux Malware & Privilege-Escalation Investigation Workspace.
Workspace Directory: [INSERT WORKSPACE DIRECTORY PATH]

TODAY'S TARGET: Day [CURRENT_DAY_NUMBER] of the Engineering Roadmap.

Reference Roadmap Document: docs/30-day-growth-plan.md
Latest Architecture & Retrospective: docs/challenge-retrospective.md and docs/future-scope.md

================================================================================
STRICT SAFETY & OPERATIONAL GUARDRAILS
================================================================================
1. READ-ONLY FIRST: Perform a working tree and repository audit before making changes.
2. PRESERVE EXISTING CODE: Do not discard, stash, reset, or overwrite unrelated user work.
3. ENVIRONMENT & SECRET PROTECTION:
   - Never print, log, or expose secrets or environment variable values.
   - Do not modify, inspect, or stage ignored environment files (e.g., frontend/.env.local).
   - Protect local developer configurations (backend port 8010 and frontend VITE_API_URL).
4. DATA & FEATURE INTEGRITY:
   - Do not delete, clear, or corrupt browser-local saved cases in localStorage.
   - Maintain the working HTML report download and browser-native PDF export.
   - Ensure deterministic rule-based analysis remains functional and reproducible.
5. NO UNAUTHORIZED GIT OR CLOUD ACTIONS:
   - Do NOT run git commit, git push, git tag, or git release unless explicitly instructed.
   - Do NOT deploy to Render or any external cloud service.
   - Do NOT run destructive commands (rm -rf, git clean -fd, git reset --hard).
6. TRUTHFUL REPORTING:
   - Report actual test outputs and command exit codes. Never claim success for tests that were not run or failed.
   - If an acceptance criterion is incomplete, report it honestly as incomplete.
7. STOP CONDITION:
   - Implement ONLY the single milestone assigned to Day [CURRENT_DAY_NUMBER].
   - Once testing and reporting are complete, STOP and wait for my review.

================================================================================
STEP-BY-STEP EXECUTION WORKFLOW
================================================================================

### STEP 1 — PRE-BUILD AUDIT & WORKING TREE REVIEW
- Check the current repository state:
  - Run: git status --short
  - Run: git branch -vv
- Confirm that working tree diffs (if any) are understood before introducing new changes.
- Review the acceptance criteria of the prior milestone to ensure foundations are intact.

### STEP 2 — MILESTONE SPECIFICATION ALIGNMENT
- Inspect the roadmap for Day [CURRENT_DAY_NUMBER].
- Extract and confirm:
  1. Goal
  2. Concrete Deliverable
  3. Acceptance Criteria
  4. Tests or Verification
- Formulate a concise, incremental implementation plan and state it clearly.

### STEP 3 — INCREMENTAL IMPLEMENTATION
- Make focused, surgical changes using explicit file paths.
- Ensure all new Python modules, FastAPI endpoints, Pydantic schemas, React components, and CSS styles follow established project conventions:
  - Backend: Async FastAPI, Pydantic v2 schemas, type annotations, descriptive docstrings.
  - Frontend: React 18, TypeScript strict typing, CSS Modules, cybersecurity HUD design tokens.
- Maintain backward compatibility with existing data structures.

### STEP 4 — AUTOMATED TESTING & REGRESSION VERIFICATION
- Run the appropriate test commands for today's milestone:
  - New milestone-specific unit/integration tests.
  - Full backend test suite: .\.venv\Scripts\python -m pytest
  - Report regression suite: node scripts/verify_reports.mjs
  - Frontend production build: npm run build (inside frontend/)
- Capture real test counts and execution times.

### STEP 5 — COMPLETION REPORT & REVIEW STOP
Provide a structured completion report covering:
1. Milestone Summary: Goal and deliverable completed for today.
2. Files Modified & Created: Explicit clickable markdown file links.
3. Acceptance Criteria Verification: Check off each criterion with factual evidence.
4. Test Results: Exact numbers of passing/failing tests and build status.
5. Incomplete Work or Blockers: Any deferred items or open questions.
6. Stop Notice: Confirm that execution has stopped and wait for human user approval.
```

---

## 2. Specialized Prompt Patterns

### A. Root Cause Debugging Prompt
Used when encountering persistent configuration or runtime mismatches (e.g., port 8010 vs 8000):

```markdown
Role: Senior Debugging Engineer.
Problem: [Describe symptom, e.g. Frontend cannot reach backend /health on port 8010].
Context:
- Backend command: [uvicorn backend.app.main:app --port 8010]
- Frontend configuration: [frontend/.env.local VITE_API_URL]
- Error output: [Paste exact console log or browser network trace]

Instructions:
1. Do not guess or suggest speculative code rewrites.
2. Formulate 3 distinct hypotheses ranked by likelihood.
3. Propose minimal read-only diagnostic commands to isolate the root cause.
4. Once evidence confirms the cause, propose the smallest possible surgical fix.
```

### B. Security Boundary Verification Prompt
Used to audit input processing for injection vulnerabilities:

```markdown
Role: Application Security Reviewer.
Component: [e.g. backend/app/services/evidence/service.py]

Audit Criteria:
1. Identify any points where untrusted user input is passed to shell commands, eval(), or exec().
2. Audit file path resolution against directory traversal attacks (Path.is_relative_to).
3. Confirm that error responses suppress raw Python tracebacks.
4. Provide pytest test cases with adversarial inputs (null bytes, path traversal sequences, shell metacharacters).
```

### C. Architectural Trade-Off Analysis Prompt
Used before selecting technical dependencies:

```markdown
Role: Software Architect.
Decision Under Review: [e.g. WeasyPrint vs Browser-Native window.print() + @media print].
Context: Windows development environment, local workstation MVP, future minimal container deployment.

Provide a comparative analysis table evaluating:
- External native dependencies (C-libraries, binaries).
- Installation and cross-platform reliability.
- Impact on Docker image footprint.
- Developer maintenance overhead.
- Final recommendation with explicit rationale.
```

---

## 3. Core Principles Learned

1. **Role and Domain Framing**: Explicitly declaring the persona (e.g. "Senior Cybersecurity Architect") anchors the model in production standards rather than beginner tutorials.
2. **Hard Operational Guardrails**: Listing prohibited actions ("Do NOT commit, push, or execute destructive commands") prevents runaway agent actions.
3. **Mandatory Verification Gates**: Prompts that require test runs before declaring completion consistently produce higher quality code with fewer subtle regressions.
4. **Surgical Scope Constraints**: Limiting prompts to a single milestone or feature prevents scope creep and keeps diffs reviewable.
