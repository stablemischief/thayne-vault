# Skill 12: Use of Funds
## Building a Credible, Institutional-Grade Use of Funds — AI Software Product Startups

---

## Why This Slide Carries More Weight Than You Think

The Use of Funds slide is not a budget. It's a **trust signal**.

Institutional investors use it to answer three questions instantly:
1. **Do they know what they're doing?** — Are the categories real and specific?
2. **Are they serious?** — Market-rate salaries, legal work, compliance = a company, not a side project.
3. **Does the capital deployment match the story?** — If you said "we're building enterprise AI" but your budget has no SOC 2, no IP protection, and no AI infrastructure, the deck contradicts itself.

For AI software companies pitching institutional investors (banks, ag lenders, insurance companies), three categories are *non-negotiable*:
- **Talent at market rate** — you can't attract AI engineers with bootstrapper pay
- **IP protection** — patents and trademarks tell investors you're building something defensible
- **SOC 2 certification** — enterprise customers *cannot* sign contracts without it; skipping it signals you don't understand the market

Miss any of these and an institutional investor's compliance team will flag the deal before it gets to committee.

---

## The Eight Categories for AI Software Product Startups

### 1. Team / Payroll (50–60% of raise)

This is always the largest line item. Show it with specifics — not just a total.

**What to include:**
- Named roles with annual salaries (founder/CEO, CTO/co-founder, AI engineers, product, sales)
- Duration: typically 18–24 months of runway baked in
- Benefits burden: add ~20–25% on top of base for benefits, payroll taxes, employer contributions

**2025–2026 market rate benchmarks (AI software startup):**
- Startup CEO (seed–Series A stage): $130,000–$175,000/year
- CTO / Technical Co-Founder: $140,000–$185,000/year
- AI Engineer / ML Engineer: $145,000–$195,000/year (commanding a premium over standard SaaS dev)
- AI-Native Developer (emerging category): $90,000–$140,000/year
- Sales / Business Development: $80,000–$120,000 base + commission structure
- Head of Customer Success (enterprise): $90,000–$130,000/year

**Chris Johnson's rule:** If your founders aren't taking market-rate salaries, institutional investors will assume you can't attract or retain talent. Show the real number. It signals you understand the company you're building.

**Investor Q&A prep:**
- "Why is the CEO salary X?" → "Market rate for a product-led startup CEO in this stage and geography. Below this, we risk distraction from secondary income sources."
- "Can you cut payroll to extend runway?" → "We've sized this to execute the roadmap. Cuts below this would require descoping the product plan, which changes the return model."

---

### 2. AI Infrastructure & Model Costs (8–12%)

This is **unique to AI software companies** and what differentiates this skill from traditional SaaS use-of-funds frameworks.

**What institutional investors expect to see:**

| Line Item | Description | Typical Range |
|---|---|---|
| LLM API costs | Anthropic Claude, OpenAI, Gemini — inference at scale | $3K–$25K/month depending on usage |
| Vector DB / embeddings | Pinecone, Weaviate, pgvector — document search and semantic retrieval | $500–$5K/month |
| AI observability tooling | LangSmith, Helicone, or similar — required for debugging and cost control | $300–$2K/month |
| Fine-tuning / model evaluation | One-time or quarterly costs to evaluate custom model performance | $5K–$50K per run |
| Data storage / compute | S3, Supabase, cloud compute beyond standard hosting | $500–$5K/month |

**The scaling problem you must address:**
AI inference costs are *variable* and can balloon as usage grows. Investors in 2025–2026 know this and will ask. Your use-of-funds should show:
1. Current cost per transaction/user at MVP scale
2. Projected cost at 10x and 100x scale (the unit economics story)
3. Why this doesn't destroy margins: model costs are ~3–8% of revenue at scale for well-architected AI SaaS

**Example talking point:**
"At our projected Year 2 volume of X transactions, LLM inference runs approximately $Y/month — roughly 4% of projected revenue. This is already baked into our margin model."

---

### 3. Product Development (10–15%)

This covers the build — separate from payroll, which covers the people doing the building.

**What to include:**
- Cloud hosting and DevOps infrastructure (AWS, Vercel, Coolify, etc.)
- Third-party SaaS tools and developer licenses
- QA, testing infrastructure, CI/CD tooling
- Design tools and UX research
- Security tooling (separate from SOC 2 certification work — see Category 5)

**What NOT to put here:**
- Developer salaries → Payroll (Category 1)
- AI API costs → AI Infrastructure (Category 2)
- Security audits for compliance → Compliance & Certification (Category 5)

---

### 4. IP Protection — Patents, Trademarks & Trade Secrets (4–7%)

**This is a critical trust signal for institutional investors.** It tells them:
- You believe you're building something defensible
- You understand the legal landscape of enterprise software
- You've thought about competitive moat beyond "we move fast"

**What belongs in this budget:**

**Patent Strategy**
- Provisional patent applications: $3,000–$8,000/each (buys 12 months of "patent pending" status while you build)
- Full utility patent application: $15,000–$35,000/each (total filing + attorney fees)
- Patent search and freedom-to-operate analysis: $5,000–$10,000 (before you file — required)
- Office action responses (if USPTO pushes back): $3,000–$8,000/occurrence

**What to potentially patent for an AI software company:**
- Proprietary model pipelines or multi-agent orchestration architecture
- Novel data extraction or classification methods
- Unique training data curation or feedback loop mechanisms
- Any algorithm or method that isn't just "we use Claude" — the *how* around it

**Note on AI patent landscape:** AI itself is not patentable, but *specific methods* implemented using AI are. Your attorney will help identify what qualifies.

**Trademark Protection**
- Federal trademark registration (USPTO): $250–$350/class/application (government fee)
- Attorney fees for filing + prosecution: $1,500–$3,500/mark
- What to register: company name, product names (Agfin, Kowerk), taglines if distinctive
- Timeline: 12–18 months from filing to registration

**Trade Secret Strategy**
- NDA enforcement templates and employee IP assignment agreements: $2,000–$5,000 (one-time legal setup)
- Internal data governance policies: typically bundled with SOC 2 prep (see Category 5)

**Total IP budget recommendation for seed/Series A AI startup:**
- Conservative: $40,000–$75,000 (2–3 trademark filings, 1–2 provisional patents, freedom-to-operate analysis)
- Aggressive: $100,000–$175,000 (4–6 patents, full trademark portfolio, ongoing counsel retainer)

**Investor Q&A prep:**
- "What can you actually patent?" → Have your attorney give you 2–3 specific answers before investor meetings. Never say "we're exploring that."
- "Why now? You don't have revenue yet." → "Filing provisional patents now costs $X and locks our priority date. Waiting 12 months after launch could cost us the ability to file at all."

---

### 5. Compliance & Certification — SOC 2 (4–6%)

**Non-negotiable for enterprise AI software.** Any bank, insurance company, Farm Credit institution, or agricultural enterprise you want to sell to will require SOC 2 Type II before signing a contract. Period.

**The SOC 2 Pathway:**

**SOC 2 Type I** (point-in-time snapshot — faster, cheaper)
- Demonstrates: your security controls *exist* and are *designed correctly* as of a specific date
- Timeline: 2–4 months from kickoff
- Cost: $15,000–$40,000 total (readiness assessment + auditor fees + remediation work)
- What it gets you: early enterprise conversations, proof you're serious about security

**SOC 2 Type II** (ongoing — what enterprise buyers actually require)
- Demonstrates: your controls *operated effectively* over a 6–12 month observation period
- Timeline: 6–12 months after Type I is complete
- Cost: $30,000–$75,000 total (auditor fees + ongoing tooling + potential remediation)
- What it gets you: signed enterprise contracts, institutional partnerships, investor confidence

**Full SOC 2 budget breakdown (Type I → Type II path):**

| Item | Cost Range | Notes |
|---|---|---|
| Readiness assessment / gap analysis | $5,000–$15,000 | Done first — identifies what you need to fix |
| Compliance automation platform | $10,000–$30,000/year | Vanta, Drata, Secureframe — dramatically reduces cost |
| Remediation (policies, controls, tooling) | $5,000–$20,000 | One-time setup, mostly internal time |
| Type I audit (external auditor) | $10,000–$20,000 | CPA firm specializing in SOC 2 |
| Type II audit (12-month window) | $20,000–$40,000 | Higher cost, more rigorous |
| Ongoing compliance tooling | $8,000–$20,000/year | Post-certification maintenance |
| **Total (Type I + II, 18–24 months)** | **$58,000–$145,000** | |

**Recommended approach for this raise:**
- Start SOC 2 Type I in Month 1–2 of deployment
- Use compliance automation (Vanta or Drata) — reduces audit fees by 30–40% and dramatically cuts internal time burden
- Time Type II certification to complete before your first enterprise contract negotiation
- Type II completion becomes a key milestone in your "Gets Us To" statement

**Investor talking point:**
"We're budgeting SOC 2 Type I within our first 6 months and Type II completion by Month 18. This certification is a prerequisite for our target enterprise customers — banks, Farm Credit institutions, insurance companies. It's not optional, and we've budgeted for it correctly."

**What NOT to say:** "We'll do SOC 2 when a customer asks for it." This signals you don't know how enterprise sales works. SOC 2 takes 12–18 months. By the time a customer asks, it's too late.

---

### 6. Sales & Market Development (8–12%)

**What to include:**
- Conference and event presence (ag-tech conferences, financial institution events, Farm Credit annual meetings)
- Marketing materials and pitch collateral (deck design, one-pagers, demo videos)
- Sales CRM and tooling
- Early sales contractor or commission-based rep (if not yet a FTE hire)
- PR and thought leadership (ghostwriting, LinkedIn, industry placement)

**AI-specific note:** In 2025–2026, many enterprise AI deals close through relationship and demonstration, not inbound marketing. Weight toward conferences, demo events, and relationship-driven outreach over broad digital marketing.

---

### 7. Legal & Corporate Governance (3–5%)

Separate from IP protection — this covers the company itself.

**What to include:**
- Corporate formation / restructuring (C-Corp conversion if not already done): $5,000–$15,000
- SAFE note and equity documentation: $3,000–$8,000
- Employee IP assignment agreements and offer letters: $2,000–$5,000
- Privacy policy, terms of service, data processing agreements (DPAs) for enterprise: $3,000–$8,000
- General legal retainer (ongoing): $500–$2,000/month

**Note on data privacy:** If your AI processes farm financial data, you may be subject to state-level ag data privacy laws. A brief legal review ($3,000–$5,000) before launch is worth the cost.

---

### 8. Operations & Reserve (3–5%)

**What to include:**
- Office / co-working space (if any)
- Travel for investor meetings, partnership visits, client meetings
- Accounting and bookkeeping
- Insurance (D&O, E&O, cyber insurance — increasingly expected by institutional investors)
- Contingency reserve: keep 5–8% unallocated as a buffer

**On cyber insurance:** As an AI company handling agricultural financial data, this is increasingly a requirement — and it's affordable at this stage ($5,000–$15,000/year for a seed-stage company).

---

## Sample Use of Funds Table

*For an $8M raise targeting 18–24 months runway:*

| Category | Amount | % | Key Milestone Unlocked |
|---|---|---|---|
| Team / Payroll (24 months) | $4,400,000 | 55% | Full execution team in place through Series A |
| AI Infrastructure & Model Costs | $720,000 | 9% | Scales with 3 deployed products |
| Product Development | $880,000 | 11% | 3 products shipped, Agfin + Kowerk + one new vertical |
| IP Protection (patents + trademarks) | $320,000 | 4% | 2–3 provisional patents filed, core marks registered |
| Compliance & Certification (SOC 2) | $400,000 | 5% | Type I by Month 6, Type II by Month 18 |
| Sales & Market Development | $640,000 | 8% | First 3–5 enterprise partnerships signed |
| Legal & Corporate Governance | $320,000 | 4% | Clean cap table, SAFE docs, enterprise-grade contracts |
| Operations & Reserve | $320,000 | 4% | Contingency + travel + insurance |
| **Total** | **$8,000,000** | **100%** | |

---

## The "Gets Us To" Statement (Required)

Every use of funds slide must end with this — the one-sentence summary of what the capital *accomplishes*.

**Template:**
"This $[X]M raises us to Series A readiness: [specific product milestone], [client/revenue milestone], and [compliance/credibility milestone] — positioning for a Series A at [valuation] within [timeframe]."

**Stable Mischief example:**
"This $8M gets us to 3 deployed AI products, SOC 2 Type II certification, $3M+ ARR from licensing and SaaS, and a clear exit thesis for institutional acquisition — positioning for a $40–60M Series A by Month 24."

---

## What NOT to Do

- **Don't use round percentages** — "25%, 25%, 25%, 25%" looks like a guess, not a plan
- **Don't hide founder salaries** — investors will ask. Show them clearly.
- **Don't skip SOC 2** — enterprise AI companies without a compliance budget are a red flag
- **Don't lump legal and IP together** — they're different stories with different investor significance
- **Don't show a pie chart without the "Gets Us To" statement** — the visual alone doesn't close deals
- **Don't say "we'll figure out compliance when customers ask"** — this disqualifies you from the enterprise market
- **Don't forget AI infrastructure costs** — underestimating LLM API costs at scale is a common mistake that kills margins and surprises investors in due diligence

---

## Investor Q&A: The Hard Questions

**"Why is X% going to payroll? Can you get more done with less?"**
→ "Market-rate talent is the only way to build and retain the team this roadmap requires. We've sized this against current AI engineer market rates. Below this, we'd be building with the wrong people."

**"Do you have any patents filed?"**
→ Have a real answer. Either "Yes — we have provisionals on [X]" or "No, but we have a patent search underway with [attorney] and expect to file [X] provisionals in Q[X]." Never "we're thinking about it."

**"When will you have SOC 2?"**
→ "We're budgeting Type I within 6 months and targeting Type II by Month 18. We're using [Vanta/Drata] to automate the process and reduce audit friction. Our first enterprise contract negotiations won't begin in earnest until Type I is complete."

**"What happens if your LLM costs exceed budget?"**
→ "We've modeled 3x headroom on AI infrastructure costs. We also architect for model-switching — we're not locked into a single provider — which gives us pricing leverage as the model market continues to commoditize."
