# RFC 0001: AI-Powered Project Status Summaries for the Dashboard

**Author**: Ashley Rice 
**Date**: 2025-08-23  
**Status**: Draft (Sample for portfolio)  
**Reviewers**: Engineering, Product, Customer Success  
**Disclaimer**: This is a fictional sample for portfolio use only.  
It is not affiliated with Monograph or any other company.  

---

## Summary
Add an AI summary box to the project dashboard.  
It generates a short status update from existing project data.  
Users can edit the text before sharing.

---

## Context
Project managers prepare frequent status updates.  
They pull numbers from budgets, schedules, and time logs.  
This work is slow and easy to get wrong.  
The product already stores the data.  
A first-draft summary can save time and reduce errors.

---

## Scope
**In scope**  
- Web dashboard only (Next.js)  
- Read-only summaries with an Edit control  
- English output  
- Single project view  

**Out of scope**  
- Mobile app support  
- Cross-project rollups  
- Client-facing templates and PDF export  

---

## Goals
- Cut time spent on status write-ups  
- Improve confidence in reported data  
- Keep the experience simple and fast  

## Non-Goals
- Remove the need for human review — not a goal  
- Fully automate status reporting — out of scope  
- Replacing project managers’ judgment — never a goal  

---

## Proposal
Add an “AI Summary” panel to the project view.  
When opened, the system composes a short update using project metrics.  
Users can regenerate or edit before saving to notes.  

### Data Used
- Budget used vs budget planned  
- Hours logged vs plan  
- Milestones: due dates and slippage  
- Staffing: planned vs actual assignment  

### Data Flow
1. Frontend calls GraphQL to request a summary  
2. Rails service fetches project metrics from Postgres  
3. Service builds a compact JSON payload  
4. Service calls the LLM API with an instruction prompt and the payload  
5. Service returns a draft summary and supporting metrics  
6. Frontend renders the draft with a link to “Data sources”  

---

## Prompt Strategy
- Use a system prompt with clear rules: professional tone, 140–180 words, three-part structure (Budget, Timeline, Staffing)  
- Provide structured inputs (metrics and labels)  
- Require explicit flags: “On track,” “At risk,” or “Off track” with a one-sentence explanation  
- For safety, forbid invented data and external facts  
- Keep outputs under 140–180 words  

---

## Technical Details
- **Frontend**: Next.js/React component, lazy-loaded  
- **Backend**: Ruby on Rails endpoint `POST /ai/summary`  
- **API**: GraphQL resolver calls the Rails service  
- **Caching**: Redis cache keyed by project + metrics hash (TTL 15 min)  
- **Database**: Postgres (no schema change)  
- **Observability**: log request IDs, latency, cache hit rate, token usage  

---

## Risks
- **User Trust**: Project managers may distrust AI outputs  
  - **Mitigation**: Make summaries editable and show source metrics  

---

## Success Metric
- Goal: 50% of active project managers adopt the AI summary feature within 3 months  

---
