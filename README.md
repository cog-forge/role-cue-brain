---
title: RoleCue Domain Brain
tags:
  - brain
  - documentation
  - architecture
  - domain-knowledge
aliases:
  - RoleCue Brain
  - SEP490 Domain Brain
---

# RoleCue Domain Knowledge Base

Welcome to the **RoleCue Domain Knowledge Base** (Brain). This repository serves as the single canonical source of durable business domain knowledge, concepts, invariants, and long-lived product decisions for the RoleCue platform.

---

## 1. Brain Philosophy

This repository is intentionally maintained as a **durable domain knowledge base**, not an operational project management tracker or a code mirror.

### What This Brain Answers
* **What is RoleCue?** The purpose, value proposition, and boundaries of the platform.
* **What problem does it solve?** Pain points in modern technical interview preparation and technical hiring practice.
* **What are its business domains?** The functional boundaries separating identity, job descriptions, job postings & applications, interview simulation, avatar & voice synthesis, evaluation, payment, and administration.
* **What are the core concepts?** Unambiguous definitions of Target JDs, Job Postings, Blueprints, Sessions, Voice Profiles, and Avatars.
* **How do those concepts relate?** Entity relationships, data flows, and conceptual cardinality.
* **What are the important product invariants and long-lived decisions?** Immutable rules that govern how the platform behaves.

### What This Brain Does NOT Answer
* Operational task progress ("What task are we doing today?", "What sprint are we in?") $\rightarrow$ Managed in **Jira**.
* Execution dependencies ("What KAN ticket depends on what?") $\rightarrow$ Managed in **Jira**.
* Git implementation state ("What PR is merged?", "What code file currently exists?") $\rightarrow$ Managed in the **Code Repository**.
* Working academic deliverables ("What report section is being edited?") $\rightarrow$ Managed in **Google Drive**.

---

## 2. Platform Summary

**RoleCue** is an AI-powered virtual technical interview simulation platform. It delivers realistic, personalized technical interview practice tailored to target Job Descriptions through:
* **AI-based JD Extraction & Refinement:** Ingestion of text or PDF job descriptions, deterministic validation of technical competencies, and natural-language candidate refinement.
* **Internal Interview Blueprint Generation:** Autonomous generation of a single core-question bank Blueprint per JD following human confirmation of extracted requirements; hidden from Candidates, but editable by Recruiters for their own Job Postings.
* **Real-Time 3D Virtual Interviewer:** Interactive WebGL avatar with real-time speech-to-text (STT), text-to-speech (TTS), and synchronized blend-shape viseme lip-sync.
* **Adaptive Technical Questioning:** Simulation conducts a random selection of core questions from the bank with bounded follow-ups; question-loop decisions and follow-up generation remain decoupled without premature vendor or single-model lock-in.
* **Post-Interview Evaluation:** Automated multi-dimensional scoring across core technical competencies with actionable gap analysis; recruiter-configurable weights for job postings.
* **Lightweight Job Posting & CV-First Application:** A Recruiter company-JD board with Admin approval, locked company interview presentation, and CV-first application screening. The Recruiter reviews submitted applications and CVs before an interview occurs; passed applicants interview using Recruiter-funded interview slots; recruitment remains bounded at Application Approve/Reject.
* **Personal 3D Avatar Inventory:** An embedded free Avaturn iframe experience whose final GLB is converted by RoleCue to a persisted VRM avatar. Both Candidates and Recruiters maintain personal avatar inventories with storage slot capacity and generation fees charged upon successful VRM persistence.
* **Coin Wallets & Slot Funding:** Coin wallets for Candidates and Recruiters (replacing memberships and subscriptions). Coins fund Candidate practice interviews (debited upon start), Recruiter interview slots, avatar generation, and avatar inventory capacity.

---

## 3. Repository Map

The knowledge base is structured into four core directories:

```text
role-cue-brain/
├── README.md
├── 00_Project/                 # Product definition, actors, flows, and terminology
│   ├── Overview.md             # Core problem, value proposition, and boundaries
│   ├── Actors-and-Capabilities.md # Actor model and finalized capability boundaries
│   ├── Core-Flows.md           # End-to-end user journeys (Practice, Application, Avatar)
│   └── Glossary.md             # Locked domain terminology and distinction rules
├── 01_Domains/                 # Deep domain specifications & invariants
│   ├── Auth/README.md          # Identity, credentials, and access control
│   ├── Job-Description/README.md # Ingestion, extraction, review, and refinement
│   ├── Job-Posting-Application/README.md # Recruiter job postings, intake management, CV screening, and candidate applications
│   ├── Interview/README.md     # Real-time simulation, configuration, and question-bank blueprint engine
│   ├── Avatar-Voice/README.md  # 3D avatar rendering, Candidate/Recruiter avatar inventory, and TTS voices
│   ├── Evaluation/README.md    # Core competencies, scoring rubrics, posting-level weights, and roadmaps
│   ├── Payment/README.md       # Coin packages, wallets, transactions, and interview/avatar slot funding
│   └── Administration/README.md # Platform governance, moderation, voice profiles, payment audits, and operational oversight
├── 02_System/                  # System-level models and architectural boundaries
│   ├── Context.md              # External actors and service boundaries
│   ├── Domain-Model.md         # Conceptual entity-relationship diagram
│   ├── Integrations.md         # LLM, STT, TTS, Payment Gateway, and Email integration
│   └── Data-Relationships.md   # Cardinalities, ownership, and cascading rules
└── 03_Decisions/               # Long-lived product and architectural decisions
    └── Product-Decisions.md    # Ratified foundational decisions and unresolved rules
```

---

## 4. Reading Guide

* **New to the project?** Start with [[00_Project/Overview|Project Overview]], review [[00_Project/Actors-and-Capabilities|Actors & Capabilities]], and read [[00_Project/Core-Flows|Core Flows]].
* **Confused about terminology?** Consult the [[00_Project/Glossary|Domain Glossary]] for strict definitions (e.g., Target JD vs. Job Posting, Extracted JD vs. Blueprint).
* **Designing or understanding a feature?** Explore the relevant domain in [[01_Domains/Auth/README|01_Domains]].
* **Examining system boundaries & integrations?** See [[02_System/Context|Context Diagram]] and [[02_System/Integrations|External Integrations]].
* **Understanding foundational architectural constraints?** Read [[03_Decisions/Product-Decisions|Product Decisions]].
