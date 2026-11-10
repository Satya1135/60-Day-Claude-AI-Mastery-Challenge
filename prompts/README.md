# Prompt Library — 60-Day Claude AI Mastery Challenge

This directory preserves prompt engineering patterns, reusable templates, and structured AI pair programming workflows developed during the challenge.

---

## Featured Prompt Resources

- **[useful-prompts.md](useful-prompts.md)** — Complete catalog of prompts developed during the challenge, including:
  - The **Reusable Daily Build Prompt Template** with safety guardrails and audit checklists.
  - Specialized prompt patterns for Root Cause Debugging, Security Audits, and Architectural Trade-Off Analysis.
- **[External Daily Build Prompt](https://github.com/Satya1135/ai-linux-investigation-workspace/blob/main/docs/daily-build-prompt.md)** — The original daily build prompt template in the capstone repository.

---

## Core Prompt Engineering Principles Learned

1. **Be specific about role, technology stack, and context** — anchoring the AI in production standards prevents tutorial-grade output.
2. **Define strict acceptance criteria** — state measurable outputs and testing requirements upfront.
3. **Set explicit negative boundaries** — explicitly state what is *out of scope* to prevent feature creep.
4. **Enforce verification gates** — require automated test execution and log review before declaring tasks complete.
5. **Request incremental output** — decompose complex full-stack features into reviewable, single-milestone steps.
