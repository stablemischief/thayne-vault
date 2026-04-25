# Competitor Landscape Research
**Last updated:** 2026-03-13

---

## Landjourney Technologies

**Source:** CTO job posting on Built In (https://builtin.com/job/cto/8619417), confirmed March 2026

**What they are:** Early-stage ag fintech building loan origination, servicing, and capital markets software for agricultural lenders. HQ Boulder CO, founded 2025, 11 employees.

**Funding:**
- $4.6M seed round raised (self-disclosed in job posting)
- Currently confidentially raising a Series A described as "much larger"

**Traction:** Within 1 year of MVP go live, serving up to $6B in loans on platform, including "the sector's largest independent lender and largest cooperative."

**Their self-description:** "the only fintech providing software to lenders in the agriculture vertical" — their words.

**Tech stack:** FastAPI backend, CockroachDB, Angular front-end

**What they have:**
- Multi-tenant loan origination, servicing, and capital markets software
- Workflow/process automation
- Rules-based lending eligibility checks
- SOC 2 Type II security

**What they are still building (per CTO job description):**
- AI-powered data extraction, data reconciliation
- Document extraction capabilities
- The CTO role explicitly lists "Data Extraction" and "Workflow Automation" as top skills needed — meaning these are gaps they're trying to fill

**How AgFin is different:**
Landjourney is building the lending workflow layer. AgFin is the document intelligence layer — multi-agent extraction with field-level arbitration, compliance-grade immutable audit trails, and analyst review workflow. These are architecturally different products that could be complementary or competitive depending on how Landjourney builds out their extraction capability.

**The narrative angle:**
Landjourney's $4.6M seed round and immediate move to Series A validates the market. Their own job posting confirms they don't yet have the document intelligence capability AgFin already has built and running. The market is moving fast. Stable Mischief is not behind — it is ahead on the specific technical capability that matters most for regulated lending workflows.

---

## iBusiness Funding / LenderAI

**What they are:** Full-stack SBA lending platform with AI workflow automation ("Lendsey" agentic AI). $11B+ processed, $100M committed to AI development.

**Gap vs. AgFin:** Chatbot/workflow-centric, not multi-agent document extraction with arbitration and audit trails. SBA-focused, not ag lending.

---

## nCino

**What they are:** Salesforce-based lending workflow orchestration platform. $516M ACV, 2,700+ financial institutions. Has signed AFSC for ag.

**Gap vs. AgFin:** Workflow and CRM layer, not document intelligence. AgFin is a complementary layer that could sit on top of nCino — potentially a partnership opportunity.

---

## Abrigo (Sageworks)

**What they are:** Ag lending templates, financial spreading, credit analysis. Widely used by community banks.

**Gap vs. AgFin:** Spreadsheet-style analysis, no AI extraction or multi-agent architecture.

---

## Landjourney Competitive Summary for Investor Conversations

**What to say:**
"Landjourney raised $4.6M in seed funding in 2025 and is already raising a Series A — their own job postings confirm they're still building the document extraction capability AgFin already has in production. That tells you two things: the market is real, and we're ahead on the capability that matters most."

**What NOT to say:**
"No one is building in this space" — Landjourney and others exist. The white space claim needs to be specific: no one has multi-agent extraction + analyst review + compliance-grade audit trail in a single ag-focused platform.
