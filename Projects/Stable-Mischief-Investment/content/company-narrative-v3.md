# Stable Mischief — Master Company Narrative
**Draft 3 | March 2026**
**CONFIDENTIAL — For review by James Whitfield and Chris Johnson before any external use**

---

## What Stable Mischief Does

Stable Mischief is an agricultural technology product company. We use AI-native development methodology to rapidly identify, build, validate, and deploy software products that help farmers generate and keep more revenue per acre.

Our first product — AgFin — is a document intelligence platform for agricultural lenders that replaces weeks of manual data entry with hours of AI-powered extraction. It is in production. It works. It is the proof that the methodology delivers.

---

## The Problem

American agriculture runs on inefficiency.

A farmer in Nebraska with 60 farm locations wants to renew his crop operating line. He gathers his FSA records, Schedule F tax returns, insurance binders, and land lease agreements. He drops them off at the bank and waits. The loan analyst opens the first document, keys the data into a spreadsheet, closes it, opens the next. For a borrower with 12 farm locations across three counties, that's a minimum of 12 FSA forms, multiple insurance policies, tax records, and entity documents — weeks of work. One Farm Credit association reported using nine separate systems to originate a single loan.[^1] Another described closing packages as "about an inch and a half thick."[^2] Many associations remain, in the words of one technology assessment, "shackled to aging, homegrown on-premises technology systems."[^3]

But the problem doesn't stop at the lender's desk.

The agronomist writing field management plans still builds them in spreadsheets. The input retailer managing supplier relationships, customer accounts, and crop planning is running the same disconnected software stack they used ten years ago. The farm service organization advising hundreds of producers on everything from soil health to equipment procurement has no institutional memory — when a veteran employee retires, their knowledge walks out the door. Across the agricultural support ecosystem, the pattern repeats: high-value expertise trapped in manual processes, served by technology that hasn't kept pace with the complexity of modern agriculture.

This isn't a niche problem. U.S. commercial banks held $205 billion in farm loans at year-end 2024.[^4] The Farm Credit System — the largest agricultural lender — holds $456.9 billion in outstanding loans across 600,000 borrowers.[^5] Federal crop insurance covers $192 billion in liability and processes more than 2.1 million policies per year.[^6] More than half of agricultural lenders only began digitizing operations in 2025.[^7]

All of it is underserved by technology. All of it is opportunity.

---

## Why Now

Three years ago, this problem didn't have a practical solution at scale. The documents were too complex, the domain too specialized, the regulatory requirements too demanding.

That is no longer true.

Agentic AI — systems where multiple AI models work in parallel, check each other's outputs, and escalate disagreements for human resolution — can now do what trained specialists do with complex documents, at the accuracy and traceability that regulated industries require. The technology crossed the threshold in 2024. The organizations that recognize this and build for it first will define the category. The window is open. It will not stay open.

The Farm Credit Administration's Cyber Risk Management rule, effective January 2025, now requires SOC 2 Type II certification, AI model risk management documentation, and data encryption for all technology vendors serving Farm Credit institutions.[^8] This is a burden for Stable Mischief has already begin structuring for— it is a barrier to entry we have already cleared. Vendors building today face 6–12 months of compliance work before they can have a conversation with a Farm Credit buyer. We have that infrastructure in production.

---

## The Products

Stable Mischief is raising as an AI product company, not for a specific product. That distinction matters more in 2026 than it ever has.

The advancement of AI is compressing product lifecycles in ways that have no historical precedent. A software product that solves a meaningful problem today could be partially or fully commoditized within months by a frontier model update or a better-funded competitor. Investors who back a single product are betting on that product's shelf life. That is not the bet we are asking anyone to make.

We are asking investors to back a company that is skating to where the puck is going. The investment is in three things: a methodology for identifying and validating the highest-value problems in agriculture faster than any traditional software company can; a growing data asset that reveals where those problems are before the market can articulate them; and a team that has demonstrated it can rapidly move from concept to production-grade platform, with fewer than six people, at a fraction of conventional development cost.

The products below are proof that the methodology works. They are the first contents of the barn. They will not be the last. What follows is the current portfolio.

### AgFin — Document Intelligence for Agricultural Lending

AgFin is a purpose-built document intelligence platform for agricultural loan origination. When documents are uploaded, AgFin classifies them, deploys parallel extraction agents — different AI models reading the same documents simultaneously — compares their outputs field by field, and uses an arbitration agent to resolve disagreements. Every decision is logged in an immutable, hash-chain audit trail. Fields below a confidence threshold are flagged for analyst review. Fields with conflicting data between agents are resolved with documented rationale. When the analyst certifies the file, the data is locked — tamper-evident and compliant.

The platform covers 1,395 structured fields across 10 lending modules, processes 9 agricultural document types, and runs through a 6-phase pipeline per application.[^9] The test suite includes 91 pipeline-specific tests. SOC 2-ready infrastructure — AES-256 PII encryption, row-level security, append-only audit trails — was built from day one, not retrofitted.

This architecture is the subject of a provisional patent application filed by Stable Mischief: *System and Method for Multi-Model Consensus-Based Document Data Extraction with Selective Field-Level Arbitration* (docket AGR-PAT-001).

What took a skilled analyst weeks to compile will now take hours (even minutes in some cases) with AgFin. At an AI processing cost of less than $5 per application versus hours of analyst time at market rates, the economics are not close.

AgFin was built in under 120 days, by a team of fewer than five people. 

### Kowerk — AI Workspace for Agricultural Organizations

The agricultural support ecosystem — agronomists, input retailers, farm service organizations, rural lenders, cooperative extension professionals — is full of expertise and short on efficient processes. These organizations know agriculture. They do not know how to leverage AI to make their operations run better. And the general-purpose AI tools available to them were not built with their workflows, their data, or their compliance concerns in mind.

Kowerk is a business AI platform designed specifically for these organizations. It provides persistent, project-based workspaces where context accumulates over time — solving the "start from zero every session" problem that makes generic AI tools inefficient for ongoing business operations. Users get a team of specialized AI assistants (strategy, legal, operational, financial, etc) that are pre-trained and context-aware, collaborating within the same project space.

The initial pricing range is $25–$100 per user per month, targeted at the small and mid-size ag organizations that will adopt it first. The 24-month product milestone includes private model deployment — an option for organizations to run AI without their operational and financial data flowing thru frontier model providers (OpenAI, Google, Anthropic, etc). In an industry built on confidentiality, that is not a minor feature. It is the reason data-sensitive ag businesses will choose Kowerk over generic AI tools.

Kowerk launches within the first six months of this funding round.

### aiBA — AI Business Analyst

Kowerk works out of the box. aiBA makes it exceptional.

Most organizations do not have their processes documented. How work gets done lives inside the heads of the people who do it. When a veteran employee leaves, that knowledge walks out the door. New hires learn by trial and error. Leadership cannot identify inefficiencies they cannot see.

aiBA is an optional, deeply discounted add-on that solves this at the front door. An AI interviewer — pre-briefed on the organization's structure and operations — conducts structured discovery sessions with the client's team, asking questions until it has a comprehensive understanding of how the business actually runs. The outputs are practical and immediate: detailed process documentation written in plain language, and visual workflow diagrams that give leadership a clear picture of how work moves across the organization.

That documentation flows directly into Kowerk as the company's foundational knowledge base. From day one, the platform has institutional memory. New employees onboard faster. Process questions have answers. The AI assistant helps teams do their documented processes better — because it knows what those processes are.

aiBA is presented to every Kowerk customer as a high-value, high-ROI starting point. It is not a requirement. It is the fastest path to getting full value from the platform.

---

## The Strategic Moat

Most software companies choose a market and hope they've identified the right problem. Stable Mischief has built a system that tells us where the problems are.

The methodology that built AgFin in less than 120 days is repeatable: identify a problem, build a working solution, validate it with real users, and decide whether to scale or move on — in weeks, not years. This compressed product cycle is the operational moat. Capital efficiency is the financial moat.

The aiBA data flywheel is the compounding moat. Every aiBA engagement produces anonymized, aggregate intelligence about how agricultural organizations operate — where the friction is, what processes break down, where technology is absent. No individual client is identifiable. But across dozens, then hundreds of engagements, patterns emerge that no competitor has access to: a ground-level map of where the agricultural support ecosystem is broken, built from real organizations doing real work. That map drives the product roadmap. Not theory. Not assumptions. Direct signal from the market itself. This is the barn of solutions — and aiBA is how we fill it.

The three current products are not a random portfolio. They are a system:

- **AgFin** establishes credibility and penetration with agricultural lenders — the institutions at the center of the ag financial ecosystem
- **Kowerk** reaches the broader agricultural support network — the organizations those lenders work with and the farmers they serve
- **aiBA** maps the entire ecosystem's pain and feeds both the knowledge base (stickiness) and the product roadmap (direction)

Each product makes the others more valuable. Each new customer makes the data more useful. This is not a collection of tools. It is an interconnected platform designed to compound.

---

## The Unique Insight

The organizations that have tried to solve the agricultural lending problem built workflow platforms first and tried to add document intelligence afterward. That sequence is wrong — and it is wrong in a way that cannot be fixed without starting over.

Stable Mischief built document intelligence first, with compliance baked into the architecture at the field level. Every data point has a provenance. The audit trail is not a feature added on top; it is the foundation. That is what regulators will require. That is what Farm Credit institutions — which operate under strict federal oversight — will pay for and will not easily move away from once it is in place.

The same principle applies to the broader platform: we are not building AI tools and hoping they fit agriculture. We are building from a deep, ground-level understanding of how agriculture actually works — informed by a founding team that farms, consults with agricultural organizations, and has spent years embedded in the client relationships that shaped every product decision.

---

## The Team

James Whitfield is a U.S. Army veteran who spent 26 years in technology after his service. Starting as a network consultant helping small businesses get online, he built a career in financial software consulting, completed an MBA at Vanderbilt, and joined Bridgestone Americas in 2014. In 2020, he co-founded Firestone Direct — a mobile tire service startup inside Bridgestone — and built the product and the team that drove the operation from zero to $19 million in its third year.

When Bridgestone's new leadership discontinued the program in 2024, James founded AIEOU — the predecessor to Stable Mischief — because he saw clearly what was coming. Small and medium businesses that fail to adopt AI in their daily operations likely will not exist in the next three to five years. That conviction is what started Stable Mischief. James sees AI as that great "equalizer" that will allow SMB's to compete equally or more efficiently against their larger and better funded competitors.

He also tends a small farm in Hendersonville, Tennessee. In 2021, he attended a talk by an Amish soil scientist at a homesteading festival and spent the years since rebuilding his two-acre property into a working regenerative farm: 2,000 square feet of garden beds (including 1,000 square feet under greenhouse), four honeybee colonies, more than 20 laying hens, and a small flock of Icelandic sheep on a rotational grazing model. All of these efforts working symbiotically together to support improved soil and quality output. He understands — in the practical, daily, financial sense — that a week's delay in planting can affect an entire growing season, and how critical it is to have the right information at the right moment. He brings that understanding into every product decision.

AgFin emerged from a deep client engagement with a major agricultural marketplace operator serving 1.5 million farmers. Learning that crop financing was one of the most painful and broken processes in agriculture, and drawing on the combined experience of the founding team and their agricultural industry relationships, the team developed the product architecture that became AgFin. That client remains a strategic partner and the first distribution channel for the platform.

Mitch Dornich ran product divisions at Verizon Wireless for over 15 years, and served as Vice President of Product at SiriusXM for nearly five years, where he led connected vehicle product teams delivering solutions to vehicle manufacturers worldwide. In 2024, he founded nine80.ai — a firm dedicated to bringing practical AI solutions to small and medium businesses. He and James had never met.

They found each other at an AI agentic workflow bootcamp in late 2024. Both had independently started companies in mid-2024 to solve the same problem from different angles. Both were invited to support as "Residents" at AI BuildLab in January 2025. When they discovered they shared not just a conviction about AI but a genuine affinity for agriculture — Mitch has a horse farm (as well as chickens, large garden and is a master beekeeper) — the question became not whether to work together but how. They folded both companies under the Stable Mischief banner and have built together since.

Mitch leads product development and has been the primary builder of AgFin since January 2026.

Tristan Cuevas spent eight and a half years at Bridgestone Americas across three roles in the Firestone Direct product organization — the same team James built. He is a proven collaborator who has already built software under pressure alongside this founding team. When this round closes, he moves to full-time as the Stable Mischief Head of Development.

Sean Buck brings a background that spans U.S. Marine Corps service, 11 years of IT leadership at Capital Group (where he led transformation of over 1,000 IT staff from waterfall to Agile/Lean), and co-founding Level Method — a fitness platform software licensed across nine countries. He also built Archon, an AI-native development agent used by the Stable Mischief team. When this round closes, he moves to full-time as the Stable Mischief CTO.

Chris Johnson is a formal advisor to Stable Mischief. He is the CEO and co-founder of Agrellus, an agricultural technology platform with a network of 1.5 million farmers, and has led companies through exits totaling over $4 billion. It was Chris who introduced James to the institutional investment landscape and who is actively opening the Farm Credit and institutional investor relationships that will determine the pace of this raise. Agrellus Financial — the crop lending division of Agrellus — is powered by AgFin, providing Stable Mischief with the same kind of embedded distribution advantage that AgWorx Lending provides for nCino in the Farm Credit system.

---

## The Market

The agricultural lending market alone is large enough to build a significant company.

**Agricultural lending:** U.S. commercial banks held $205 billion in farm loans at year-end 2024.[^4] More than 300,000 new agricultural loans are originated per year. More than half of ag lenders only began digitizing in 2025.[^7]

**Farm Credit System:** 55 associations holding $456.9 billion in outstanding loans, serving 600,000+ borrowers, generating $7.975 billion in net income in 2025.[^5] Technology decisions made at the service corporation level reach 6–16 associations simultaneously. A single service corporation deal is a one-to-many sales motion. The system is actively consolidating — from 69 associations in 2019 toward approximately 20 — and every merger creates a technology rationalization window.[^10]

**Crop insurance:** 12 Approved Insurance Providers, 2.1 million policies per year, $192 billion in covered liability.[^6] The same FSA-578s and acreage reports AgFin already processes. The disaster-year backlog — when claims volume spikes 5–10x regionally — is a solved problem with AgFin's architecture.

**SBA lending:** 84,400 loans totaling $44.8 billion in FY2025.[^11] Structured government forms, natural expansion once the ag lending foundation is established.

AgFin's architecture is config-driven. Expanding to a new loan type or document set is a configuration change, not a rebuild.

Beyond lending, the agricultural support ecosystem — agronomists, input retailers, farm service organizations, co-ops — represents the addressable market for Kowerk and aiBA. These organizations are the connective tissue between farmers and financial institutions, and they are almost entirely underserved by purpose-built technology.

---

## The Business Model

**AgFin** generates revenue on a per-application basis — lenders pay for each loan application processed. This outcome-based model scales with their business and mirrors how comparable platforms like Ocrolus price their services. As AgFin deepens into a full credit solution, the model matures to annual SaaS licensing.

**Kowerk** operates on a SaaS model at $25–$100 per month, priced for the small and mid-size ag organizations that will adopt it first. Team tiers serve larger organizations.

**aiBA** charges a one time $2,500 service fee for the AI-led discovery engagement, with ongoing Kowerk subscription revenue as the natural conversion. The anonymized process data accrues to Stable Mischief as a proprietary strategic asset that informs the product roadmap over time.

---

## The Competitive Position

The dominant loan origination platform in Farm Credit — nCino — has real traction with associations including Texas Farm Credit, GreenStone, and American AgCredit. It is a workflow platform. It orchestrates the movement of documents through approval stages. The risk for AgFin is not competitive displacement by nCino — it is the buyer saying "we already have nCino, why do we need this?" The answer is that workflow orchestration and document intelligence are different problems. nCino moves loans through stages. AgFin reads the documents inside those loans. They address different layers of the same process — complementary, not competing.

Abrigo and Baker Hill + FINPACK are entrenched in community and regional bank credit analysis. They own trust and budget in those institutions. AgFin's wedge is the same: not a replacement for their analytical workflows, but the extraction and auditability layer underneath them that those platforms do not provide.

Landjourney Technologies raised $4.6 million in seed funding in 2025 and is already raising a Series A within their first year — confirmation that institutional capital is moving into this market and that the $7.5–$10 million Stable Mischief is seeking is calibrated correctly. Their own hiring confirms they are building workflow first and document intelligence second. AgFin is already where they are trying to go.

No competitor currently combines parallel multi-agent extraction, field-level arbitration, analyst review workflow, and an immutable compliance audit trail in a single ag-focused platform. That is the defensible claim. Not that no one is in the market — the market is real and competitors are entering it — but that no one has built the combination that regulated agricultural lending actually requires.

---

## The Ask

Stable Mischief is raising $7.5–$10 million.

The capital funds the team — competitive salaries for the founding team and incoming developers, at market rates — and executes on the 24-month plan:

**By month 6:** AgFin live with paying lender clients. Kowerk in strong beta with ag support organizations. aiBA in active client engagements.

**By month 24:** AgFin deployed with 7–10 lender clients, generating $700K–$1.5M+ in annual per-application revenue. Kowerk broadly adopted across agricultural support organizations with private model deployment roadmap underway. Ag lending marketplace in beta — the foundation for an origination-based revenue model at a fundamentally different scale. aiBA process data beginning to paint a picture of the highest-value problems remaining in the agricultural support ecosystem. Platform positioned for first annual license conversations and Series A readiness.

The use of funds: team salaries and scaled development capacity; Farm Credit pilot programs and sales infrastructure; SOC 2 Type II audit completion (required for Farm Credit vendor approval[^8]); provisional patent filings before public disclosure at industry events.

---

## The Exit

Stable Mischief is building a company. The exit is not the strategy — it is the eventual outcome of executing the strategy well.

The institutions that will ultimately acquire or invest at scale are already known. The Farm Credit System generates $7.975 billion in annual net income and is actively investing in technology modernization.[^5] Crop insurance providers process $192 billion in liability with no modern document intelligence layer.[^6] Agricultural banks are competing on underwriting speed. These institutions do not build AI-native document intelligence. They buy it.

The comparable outcomes are real: Granular acquired by Corteva. AgriWebb acquired by Nutrien. In adjacent industries: nCino built vertical lending software and reached a $2.96 billion market cap. Blend Labs built mortgage document processing and reached $740 million. Ocrolus — the closest architectural parallel, financial document automation — is valued at over $500 million.

When Stable Mischief reaches sufficient scale, two outcomes are on the table: acquisition by one of the agricultural industry's largest institutions, or growth to the point where a public offering is viable. Both require the same foundation — category-defining products, expanding market share, and compounding revenue built on a platform that the agricultural industry genuinely needs.

We are building that foundation. The first proof is running in production.

---

## Footnotes — Verification Status

[^1]: **VERIFIED.** nCino press release, September 2020: "Texas Farm Credit Adopts nCino Platform." "Front line employees previously utilized nine separate systems to originate and close a single loan." Source: https://investor.ncino.com/news-releases/news-release-details/texas-farm-credit-adopts-ncino-platform-digitally-enhance

[^2]: **VERIFIED.** DocuSign Customer Story — GreenStone Farm Credit Services. Closing packages described as "about an inch and a half thick." Source: https://www.docusign.com/customer-stories/greenstone-farm-credit-services-transforms-agricultural-lending-with-docusign-clm-and-ncino

[^3]: **VERIFIED.** West Monroe — "Cultivating a New Season for Digital Transformation in Farm Credit." Source: https://www.westmonroe.com/insights/digital-transformation-farm-credit

[^4]: **VERIFIED.** ABA 2024 Farm Bank Performance Report. "U.S. banking industry held $205 billion in farm loans by the end of 2024." Source: https://www.aba.com/about-us/press-room/press-releases/2024-farm-bank-performance-report

[^5]: **VERIFIED.** Farm Credit System 2025 Annual Information Statement. 55 associations, $456.9 billion in loans, 600,000+ borrowers, $7.975 billion combined net income. Source: Farm Credit Administration Major Financial Indicators, https://www.fca.gov/bank-oversight/major-financial-indicators

[^6]: **VERIFIED.** USDA Risk Management Agency data, 2024. $192 billion in covered liability, 2.1M+ policies/year, 12 Approved Insurance Providers. Source: USDA RMA official listings.

[^7]: **VERIFIED.** ABA/Farmer Mac Agricultural Lender Survey, 2025. "More than half" of institutions implemented digitization efforts in 2025. Source: https://www.aba.com/about-us/press-room/press-releases/ag-lender-survey-2025

[^8]: **VERIFIED.** FCA Cyber Risk Management Rule, effective January 1, 2025. Published Federal Register December 11, 2023. Source: Federal Register 88 FR 85728, https://www.federalregister.gov/documents/2023/12/11/2023-27102/cyber-risk-management

[^9]: **INTERNAL — do not cite externally without validation.** $1.24/application processing cost is based on internal platform cost modeling. Verify before external use.

[^10]: **VERIFIED.** Farm Credit System consolidated from 69 associations (2019) to 55 (2024). "Consolidation down to 20 or even fewer associations over the next few years is quite conceivable." Source: ABA Banking Journal, September 2024. https://bankingjournal.aba.com/2024/09/farm-credit-watch-continuing-consolidation-within-fcs-increases-likelihood-of-a-fcsic-taxpayer-bailout/

[^11]: **VERIFIED.** SBA FY2025 data: 84,400 7(a) and 504 loans totaling $44.8 billion. Source: https://www.sba.gov/article/2025/09/30/trump-sba-delivers-record-capital-small-businesses-fy25

---

*Draft 3 — CONFIDENTIAL. For review only. Not for external distribution.*
*Next: James Whitfield and Chris Johnson review. No external use before approval.*
