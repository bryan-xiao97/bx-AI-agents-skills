# Sell-Side M&A Platform — Technical Design Spec
_Date: 2026-05-19 | Author: Bryan Xiao_

---

## Problem Statement

Solomon Partners' sell-side M&A workflow involves significant manual, repetitive work across buyer research, due diligence response, and buyer relationship tracking. This platform design defines a shared technical foundation that supports three AI-powered modules — Buyer's List Creation, DD Q&A Management, and Buyer Log Automation — while ensuring bankers retain ownership of judgment and direction.

## Scope

This document is a **platform-level technical design** spanning all three modules. Each module has (or will have) its own product spec and work items. This spec defines the shared infrastructure, cross-module data flows, and build sequencing.

**In scope:**
- Shared data connectivity layer (M365 MCP, Fabric/Dataverse, VDR staging)
- Per-module agent/app layer decisions
- Cross-module data flows including the Buyer's List → Buyer Log handoff
- Integration points and build sequencing

**Out of scope:**
- Module-level feature specs (covered in individual PRDs)
- DealCloud silver-layer semantic model design (owned by Max/Idris)
- VDR vendor evaluation or procurement

---

## Platform Architecture

The platform has two stable layers and one flexible layer.

```
┌─────────────────────────────────────────────────────────────────┐
│  AGENT / APP LAYER  (flexible — varies by module)               │
│  Power Apps Canvas App (unified front-end)                      │
│  Copilot Studio  |  Azure AI Foundry  |  Power Automate        │
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│  DATA CONNECTIVITY LAYER  (shared platform)                      │
│                                                                  │
│  M365 MCP          Fabric Data Agent     Dataverse               │
│  (read)            (read — DealCloud     (read/write)            │
│                     via silver layer)                            │
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│  DATA SOURCES                                                    │
│  SharePoint  |  Outlook  |  Teams  |  DealCloud  |  VDR staging │
└─────────────────────────────────────────────────────────────────┘
```

### Module Summary

| Module | Agent/App Layer | Primary Data Read | Primary Data Write |
|---|---|---|---|
| Buyer's List | Copilot Studio (existing) | M365 MCP, Fabric/DealCloud | Dataverse (buyer records), Excel/Word export |
| DD Q&A | Azure AI Foundry + Power Automate | M365 MCP (incl. VDR staging site), Dataverse | Dataverse (Q&A log) |
| Buyer Log | Power Automate + Power Apps | M365 MCP (Outlook, Teams), Dataverse | Dataverse (log records) |

---

## Data Connectivity Layer

### M365 MCP (read)

Single shared connector providing all modules with read access to SharePoint, Outlook, and Teams. The VDR integration runs through this layer via a deal-specific SharePoint staging site.

**VDR staging pattern:**

```
VDR (Intralinks / Ansarada / Datasite)
         │
         │  deal team stages relevant docs at engagement start
         │  (manual initially; Power Automate automation if VDR API supports it)
         ▼
Deal SharePoint Site  ──►  M365 MCP  ──►  DD Q&A Agent
(folder structure mirrors VDR paths                reads like any
 for citation traceability)                        other SharePoint
```

The staging site mirrors the VDR folder structure so document citations in agent responses map back to VDR source paths.

### Fabric Data Agent / Silver Layer (read)

Shared connected agent sitting over the DealCloud silver-layer semantic model (Max/Idris). All modules requiring DealCloud data call this agent — no module connects to DealCloud directly. RLS is enforced at the semantic model level, ensuring consistent access control regardless of which module is calling.

### Dataverse (read/write)

The operational data store for the platform. Core tables:

| Table | Written By | Read By |
|---|---|---|
| Deals | Manual / Power Apps | All modules |
| Buyers | Buyer's List (via PA flow) | Buyer Log, DD Q&A |
| BuyerCommunications | Buyer Log (PA auto-recording) | Buyer Log (Power Apps) |
| QALog | DD Q&A (via PA flow) | DD Q&A (Power Apps) |
| MeetingSummaries | Meeting capture workflow | Copilot agents (existing) |

---

## Agent / App Layer

### Unified Front-End: Power Apps Canvas App

A single Power Apps canvas app is the deal-team interface for all three modules. Each module is a screen within the app. Agents and automation run as backend action providers — bankers interact with a structured app UI, not a chat window. Copilot Studio and Azure AI Foundry are execution environments only; they have no user-facing interface.

```
┌──────────────────────────────────────────────────┐
│         Power Apps Canvas App                    │
│  ┌────────────┐ ┌──────────┐ ┌────────────────┐  │
│  │ Buyer's    │ │  DD Q&A  │ │   Buyer Log    │  │
│  │ List       │ │          │ │                │  │
│  └─────┬──────┘ └────┬─────┘ └───────┬────────┘  │
└────────┼─────────────┼───────────────┼────────────┘
         │             │               │
         ▼             ▼               ▼
  Copilot Studio   Azure AI        Power Automate
  Agent (via PA    Foundry RAG     flows + Dataverse
  flow or HTTP)    (via PA flow    (direct connector)
                   or HTTP)
```

### Per-Module Backend

**Buyer's List — Copilot Studio (existing)**

Orchestrator + Research Agent pattern already built and in deployment (ADO Epic 4031). The platform view adds a Dataverse write step to the existing output flow and a Power Apps screen for display and export trigger.

**DD Q&A — Azure AI Foundry**

RAG over a large heterogeneous document corpus (VDR staged documents, client emails, meeting notes, prior DD materials). Copilot Studio's M365 MCP search is insufficient for large-corpus retrieval where precise chunk retrieval and reranking matter. Azure AI Foundry indexes the deal SharePoint site and M365 content; Power Automate calls the Foundry endpoint and writes Q&A records to Dataverse.

**Buyer Log — Power Automate + Power Apps**

Two components:

- _Power Automate (background auto-recording):_ monitors Outlook and Teams for communications with tracked buyers; AI Builder summarizes threads; writes communication event records to Dataverse.
- _Power Apps (foreground banker UI):_ displays buyer log per deal (contact, firm, status, last touch, next steps); bankers update status and notes; surfaces overdue follow-ups past a defined contact threshold.

---

## Data Flows

### Buyer's List

```
1. Banker inputs deal context in Power Apps (target company, buyer criteria)
2. Power Apps triggers Power Automate flow
3. Flow calls Copilot Studio orchestrator
4. Research agent iterates:
   - M365 MCP: emails, SharePoint, Teams (internal signals)
   - Fabric Data Agent: DealCloud deal history (RLS enforced)
   - Web search: external buyer candidates
5. Orchestrator returns structured buyer list JSON
6. Flow writes buyer records to Dataverse (one per buyer, status = "Identified / Not Contacted", linked to deal ID)
7. Flow returns result to Power Apps → displayed as structured table
8. Banker triggers export → Power Automate generates Excel/Word → delivered via SharePoint or email
```

### DD Q&A

```
1. Banker selects deal in Power Apps → types due diligence question
2. Power Apps triggers Power Automate flow
3. Flow calls Azure AI Foundry RAG engine:
   - Retrieves relevant chunks from deal SharePoint site (VDR staged docs + client files)
   - Retrieves relevant chunks from M365 (emails, meeting notes, Teams)
   - Optionally queries Dataverse for DealCloud deal context
4. Foundry synthesizes answer + source citations
5. Flow writes Q&A record to Dataverse (question, answer, citations, banker, timestamp)
6. Flow returns answer + citations to Power Apps → displayed in-app
```

### Buyer Log

```
Background (automated):
1. Power Automate monitors Outlook + Teams for communications with tracked buyers
2. On trigger: AI Builder summarizes thread
3. Writes communication event record to Dataverse (buyer ID, date, direction, summary, banker)

Foreground (banker-driven):
4. Banker opens Power Apps → views buyer log for deal
5. Banker updates status, adds notes, sets next steps → writes to Dataverse
6. Power Apps surfaces overdue follow-ups (no contact past defined threshold)
```

### Cross-Module: Buyer's List → Buyer Log Handoff

The Buyer Log is seeded from the Buyer's List output. When the Buyer's List flow writes buyer records to Dataverse (step 6 above), those records become the initial buyer universe for the Buyer Log. As communications are auto-recorded, they attach to the buyer records already in Dataverse. Bankers can also add buyers manually to the log for buyers not surfaced by the agent.

---

## Integration Points

| System | Direction | Used By | Notes |
|---|---|---|---|
| M365 MCP | Read | All modules | SharePoint, Outlook, Teams; single shared connector |
| Fabric Data Agent / Silver Layer | Read | Buyer's List, DD Q&A | DealCloud via semantic model; RLS enforced; owned by Max/Idris |
| Dataverse | Read/Write | All modules | Buyer records, Q&A log, communication events, buyer log |
| Azure AI Foundry | Read | DD Q&A | RAG engine; called via Power Automate or HTTP connector |
| Copilot Studio | Execution | Buyer's List | Orchestrator + research agents; called via Power Automate or HTTP |
| VDR (Intralinks / Ansarada / Datasite) | Staged | DD Q&A | Deal teams export relevant docs to deal SharePoint site at engagement start |
| AI Builder | Read | Buyer Log | Summarizes Outlook/Teams threads before writing to Dataverse |
| Office (Excel / Word) | Write | Buyer's List | Export output generated by Power Automate |

---

## Build Sequencing

The data connectivity layer must be established before any module goes to production.

```
Phase 1 — Shared platform (prerequisite for all modules)
  - Dataverse schema: deals, buyers, Q&A log, communication events
  - M365 MCP connector configured and validated
  - Fabric Data Agent / silver layer (in progress — Max/Idris)
  - Deal SharePoint site template (VDR staging folder structure)

Phase 2 — Buyer's List (in progress — ADO Epic 4031)
  - Add Dataverse write step to existing orchestrator output flow
  - Power Apps screen: buyer list display + export trigger

Phase 3 — Buyer Log
  - Power Automate flows: Outlook/Teams auto-recording → Dataverse
  - Power Apps screen: buyer log display, status/notes editing, overdue alerts
  - Reads from Dataverse buyer records seeded by Buyer's List

Phase 4 — DD Q&A
  - Azure AI Foundry index over deal SharePoint site
  - Power Automate flow: call Foundry → write Q&A log to Dataverse
  - Power Apps screen: Q&A interface + citation display
  - Depends on VDR staging pattern being operationalized
```

DD Q&A is Phase 4 because it depends on the VDR staging pattern being operationalized and Azure AI Foundry indexing being stood up — both have more unknowns than Buyer Log.

---

## Open Questions

- What is the Dataverse environment strategy — shared environment for all deals, or per-deal environments? Affects RLS design and data isolation.
- How is the deal SharePoint site provisioned — manually per engagement, or automated via Power Automate on deal creation?
- What is the VDR staging SLA — how quickly must documents appear in the deal SharePoint site after being uploaded to the VDR? Does this need automation or is manual staging acceptable for the pilot?
- Does the Power Apps canvas app require row-level security to scope deal data per banker, or do all deal-team members see all deals?
- Which VDR vendor(s) are in use at Solomon Partners today? Determines whether VDR staging automation is feasible (API availability varies by vendor).
