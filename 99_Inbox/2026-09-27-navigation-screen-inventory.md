# RoleCue Screen Inventory & Navigation Flow Research
**Document Version:** 1.0 (Staging & Research Pass)  
**Date:** 2026-09-27  
**Status:** DRAFT / PENDING HUMAN REVIEW & FIGJAM DRAWING HANDOFF  
**Scope Boundary:** Registered Capstone Product Scope (57 Formal Use Cases)  
**Deliverable Purpose:** Authoritative screen inventory and navigation topology specifications to hand off directly to FigJam drawing agents.

---

## 1. Executive Summary & Audit Methodology

This document establishes the canonical screen inventory and navigation topology for the **RoleCue** virtual AI technical interview simulation platform. It synthesizes evidence across the canonical project Brain, finalized domain models, ratified use cases, Main Business Flows, existing Next.js frontend routes, historical Figma/FigJam design specifications, and public competitor navigation patterns.

### 1.1. Hierarchy of Source Authority

Evidence was gathered and reconciled strictly following this hierarchy:
1. **Latest Canonical RoleCue Brain** (`00_Project/`, `01_Domains/`, `02_System/`, `03_Decisions/`): Absolute authority for product boundaries, entities, and domain rules.
2. **Finalized 57 Use Cases**: Authoritative functional catalog for Candidate, Recruiter, Administrator, and Registered User.
3. **Finalized Main Business Flows**: Practice Flow (Flow A), Job Application Flow (Flow B), and Personal Avatar Generation Flow (Flow C).
4. **Ratified Product Decisions**: Hidden Blueprint rule, Job Posting as company JD, binary Approve/Reject recruitment boundary, embedded free Avaturn iframe with VRM persistence, Admin TTS voice curation without 3D mesh catalog management, no credit wallets, and Better Auth authentication model.
5. **Existing Frontend Implementation** (`ai-interview-practice/frontend`): Route structure, Next.js page.tsx templates, layout trees, and navigational configs (historical baseline).
6. **Existing Design Specifications** (`ai-interview-practice-design`): `SCREEN-INVENTORY.md`, `SCREEN-NOTES.md`, and `flows/` diagrams (historical design baseline).
7. **Competitor Navigation Patterns** (`Final Round AI`, `interviewing.io`, `LeetCode`, `Exponent`): Reference UX patterns only; never used to create unapproved product scope.

> [!CAUTION]
> **Quarantine Compliance Notice:** The directory `/home/dorriss/Documents/SEP490/example-not-follow/` was strictly quarantined and never read, inspected, or referenced during this research pass.

---

## 2. Internal Source Audit: Canonical vs. Prior Work

A comprehensive audit between the latest canonical Brain contracts and prior repository artifacts (`ai-interview-practice` frontend and `ai-interview-practice-design`) reveals crucial architectural evolutions:

```text
┌───────────────────────────────────────┬─────────────────────────────────────────────────────────────┐
│ Historical Artifact (Code / Design)   │ Canonical Ratified Contract (Current Truth)                 │
├───────────────────────────────────────┼─────────────────────────────────────────────────────────────┤
│ Blueprint Preview & Confirmation      │ STRICTLY INTERNAL & HIDDEN. Candidate never views, edits,   │
│ (/interviews/new/blueprint)           │ or confirms the Blueprint. No preview screen permitted.    │
├───────────────────────────────────────┼─────────────────────────────────────────────────────────────┤
│ Admin Avatar Management               │ EXCLUDED FROM ADMIN. 3D Avatars and Room Environments are   │
│ (/admin/avatars, detail/edit)         │ built-in platform presets. Admin curates Voice Profiles     │
│                                       │ sourced from external TTS providers only.                   │
├───────────────────────────────────────┼─────────────────────────────────────────────────────────────┤
│ Practice Credit Balances & Packages   │ EXCLUDED. Platform monetization is governed strictly by     │
│ (/billing credits, purchase credits,  │ Candidate Membership Subscriptions and recorded Payment     │
│ setup-credits-gate, system/no-credits)│ Transactions. No per-interview credits or credit wallets.   │
├───────────────────────────────────────┼─────────────────────────────────────────────────────────────┤
│ Public Marketing Pricing Page         │ EXCLUDED FROM GUESTS. Guests can only View Landing Page and │
│ (/pricing)                            │ Register. Membership options belong to authenticated users. │
├───────────────────────────────────────┼─────────────────────────────────────────────────────────────┤
│ Missing Recruiter Role                │ RATIFIED PRIMARY ACTOR. Recruiter creates Job Postings      │
│ (Entire role absent in prior design)  │ (company JDs), configures company 3D interviewer & voice,   │
│                                       │ and reviews incoming applications to binary Approve/Reject. │
├───────────────────────────────────────┼─────────────────────────────────────────────────────────────┤
│ Missing Candidate Job Board           │ RATIFIED SCOPE. Candidates browse approved Job Postings,    │
│ (Absent in prior design)              │ upload CV/resume, take the required locked interview, and   │
│                                       │ submit Application with attached Interview Result.          │
├───────────────────────────────────────┼─────────────────────────────────────────────────────────────┤
│ Custom Avatar Creation Excluded       │ RATIFIED SCOPE. RoleCue embeds free Avaturn iframe for      │
│ (Old Report 1 exclusion EX-08)        │ photo-to-3D personal avatar creation, converts final GLB to │
│                                       │ VRM, and persists Candidate-owned VRM avatar.               │
└───────────────────────────────────────┴─────────────────────────────────────────────────────────────┘
```

---

## 3. Competitor Navigation Pattern Analysis

Public documentation and user navigation patterns of products named in canonical RoleCue documentation were audited to discover established navigation conventions.

### 3.1. Products Inspected
1. **Final Round AI** (Named in canonical project records)
   - *Flow Observed:* Landing $\rightarrow$ Sign In $\rightarrow$ Dashboard / Goal Setup (Target Role, Company, JD Text) $\rightarrow$ Resume Upload $\rightarrow$ Mock Interview Configuration $\rightarrow$ Live Audio Interview $\rightarrow$ Turn-by-Turn Feedback $\rightarrow$ Automated Post-Session Debrief.
   - *RoleCue Takeaway:* Validates separating Target JD ingestion from session configuration, followed by live speech simulation and rich post-interview diagnostic debriefing.
2. **interviewing.io** (Named in canonical project records)
   - *Flow Observed:* Public Landing $\rightarrow$ Auth $\rightarrow$ Dashboard / Practice Hub $\rightarrow$ Select Topic / Track $\rightarrow$ On-Demand AI Mock Interview $\rightarrow$ Live Technical Dialogue $\rightarrow$ Session Transcript & Evaluative Feedback.
   - *RoleCue Takeaway:* Demonstrates clean transition from dashboard to readiness check to live simulation room, terminating in detailed feedback review.
3. **LeetCode** (Referenced in canonical Overview)
   - *Flow Observed:* Problem Set Catalog $\rightarrow$ Problem Detail $\rightarrow$ Code Editor $\rightarrow$ Submission Evaluation.
   - *RoleCue Takeaway:* RoleCue differentiates by rejecting static problem puzzles in favor of full JD-tailored conversational spoken interviews.
4. **Exponent (Aced)** (External reference pattern)
   - *Flow Observed:* Dashboard $\rightarrow$ Practice Module $\rightarrow$ Device / Audio Setup Preflight $\rightarrow$ Live Session $\rightarrow$ Rubric-Based Post-Interview Feedback.
   - *RoleCue Takeaway:* Validates a dedicated preflight audio/mic readiness step prior to entering the live simulation environment.

### 3.2. RoleCue Evidence vs. Competitor Patterns

| UX Pattern | Source | RoleCue Implementation Status |
|:---|:---|:---|
| **Landing $\rightarrow$ Auth $\rightarrow$ Role Dashboard** | Universal Industry Pattern | **Adopted:** Clean separation into Guest, Candidate, Recruiter, and Admin portals. |
| **JD Ingestion $\rightarrow$ Structured Extraction Review** | Final Round AI / RoleCue Brain | **RoleCue Native Contract:** Candidate reviews AI-extracted technical tags and adds Refinement Notes. |
| **Internal Assessment Blueprint** | RoleCue Exclusive Innovation | **RoleCue Native Invariant:** System compiles blueprint internally; completely hidden from Candidate. |
| **Preflight Audio & Readiness Check** | Exponent / interviewing.io | **RoleCue Native Contract:** Microphone check, audio playback check, WebGL capability check. |
| **Live 3D Spoken Dialogue with Lip-Sync**| RoleCue Exclusive Innovation | **RoleCue Native Contract:** 3D WebGL avatar with blend-shape lip-sync; graceful 2D waveform fallback. |
| **5-Competency Radar & Learning Roadmap**| RoleCue Exclusive Innovation | **RoleCue Native Contract:** Formative diagnostic evaluation across 5 standardized technical dimensions. |
| **Lightweight Application with Attached Interview Result** | RoleCue Brain / Core Flows | **RoleCue Native Contract:** Job Posting required interview result attached to CV before recruiter review. |
| **Embedded Avaturn 3D Avatar Creation** | RoleCue Product Decision | **RoleCue Native Contract:** Free Avaturn iframe embed $\rightarrow$ RoleCue GLB-to-VRM conversion. |

---

## 4. Screen Classification Framework

Every user-facing interface element is classified into one of two operational UI types:

* **FULL SCREEN (`PAGE`):**
  Deserves its own dedicated route/URL and primary viewport canvas (e.g., `/dashboard`, `/interviews/[id]/room`, `/reports/[id]`). Represents a major destination or focused workspace.
* **SUBVIEW (`SUBVIEW`):**
  A modal dialog, slide-out drawer, multi-step wizard tab, embedded iframe panel, or secondary state within a parent full screen that does not require an independent URL entry point (e.g., Change Password Modal, Turn Critiques Drawer, Avaturn Iframe Embed).
* **SYSTEM STATES & INTERNAL CONCEPTS (Non-Navigable):**
  - *System States (Error/Loading):* Generic 404, 403, 500, and loading skeletons are route boundary states, not discrete navigational nodes.
  - *Internal Concepts:* Interview Blueprint compilation, LLM next-Question analysis, and background session cleanup are strictly backend processes with no user-facing screen.

---

## 5. Canonical RoleCue Screen Inventory

### 5.1. Provisional Namespace Notation
- **`PUB-xx`**: Public Guest screens.
- **`AUTH-xx`**: Authentication & Password Recovery screens.
- **`SHARED-xx`**: Shared account settings accessible by authenticated users.
- **`CAN-xx`**: Candidate-exclusive screens.
- **`REC-xx`**: Recruiter-exclusive screens.
- **`ADM-xx`**: Administrator-exclusive screens.

### 5.2. Master Screen Inventory Table

| Provisional ID | Screen Name | Role | UI Type | Purpose | Entry From | Navigates To | Evidence | Confidence | Decision |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| **PUB-01** | Public Landing Page | Guest | FULL SCREEN | Showcase value proposition, core pillars, call-to-actions | Direct URL (`/`) | AUTH-01, AUTH-02 | Brain, Route, Design | HIGH | KEEP |
| **AUTH-01** | Account Registration | Guest | FULL SCREEN | Register account, select role (Candidate or Recruiter), email/password | PUB-01, AUTH-02 | AUTH-01-SUB1, AUTH-02 | Brain, Route, Design | HIGH | KEEP |
| **AUTH-01-SUB1** | Email Verification Notice | Guest | SUBVIEW | Inform user of verification email dispatch, option to resend | AUTH-01 | AUTH-02 | Brain, Design | HIGH | KEEP |
| **AUTH-02** | Account Login | Registered User | FULL SCREEN | Authenticate via Better Auth credentials, redirect to role hub | PUB-01, AUTH-01 | CAN-01, REC-01, ADM-01, AUTH-03 | Brain, Route, Design | HIGH | KEEP |
| **AUTH-03** | Forgot Password Request | Registered User | FULL SCREEN | Request password reset token via registered email | AUTH-02 | AUTH-03-SUB1, AUTH-02 | Brain, Route, Design | HIGH | KEEP |
| **AUTH-03-SUB1** | Reset Link Sent Notice | Registered User | SUBVIEW | Confirmation feedback that recovery link was dispatched | AUTH-03 | AUTH-02 | Brain, Design | HIGH | KEEP |
| **AUTH-04** | Set New Password | Registered User | FULL SCREEN | Set replacement password via emailed token link | Email Link (`/reset-password`) | AUTH-02 | Brain, Route, Design | HIGH | KEEP |
| **SHARED-01** | User Profile | Registered User | FULL SCREEN | View and edit personal profile details and role-specific metadata | App Navigation | SHARED-02, Role Dashboard | Brain, Route, Design | HIGH | KEEP |
| **SHARED-02** | Account Security & Password | Registered User | FULL SCREEN | Manage account security, password replacement, and 2FA | SHARED-01, Navigation | SHARED-02-SUB1, SHARED-02-SUB2 | Brain, Route, Design | HIGH | KEEP |
| **SHARED-02-SUB1**| Change Password Modal | Registered User | SUBVIEW | Form to update password using known current password | SHARED-02 | SHARED-02 | Brain, Use Case | HIGH | KEEP |
| **SHARED-02-SUB2**| Two-Factor Auth Setup Modal | Registered User | SUBVIEW | Enable 2FA with authenticator app QR code and verification token | SHARED-02 | SHARED-02 | Brain, Use Case | HIGH | KEEP |
| **CAN-01** | Candidate Dashboard | Candidate | FULL SCREEN | Primary workspace hub: recent sessions, quick practice, applications | AUTH-02, Navigation | CAN-02, CAN-03, CAN-06, CAN-09, CAN-10, CAN-11, CAN-14, CAN-15 | Brain, Route, Design | HIGH | KEEP |
| **CAN-02** | Target JD Library | Candidate | FULL SCREEN | Manage personal library of saved, reviewed Target Job Descriptions | CAN-01, Navigation | CAN-03, CAN-04, CAN-06 | Brain, Use Case | HIGH | KEEP |
| **CAN-03** | Add Target JD | Candidate | FULL SCREEN | Ingest raw JD via text paste or PDF file upload | CAN-01, CAN-02 | CAN-03-SUB1, CAN-04 | Brain, Route, Design | HIGH | KEEP |
| **CAN-03-SUB1** | AI Extraction In-Progress | Candidate | SUBVIEW | Intermediate processing indicator during LLM extraction | CAN-03 | CAN-04 | Brain, Design | HIGH | KEEP |
| **CAN-04** | Review & Refine Extracted JD | Candidate | FULL SCREEN | Inspect AI competency tags, adjust seniority, add refinement notes | CAN-03, CAN-02 | CAN-06, CAN-02 | Brain, Route, Design | HIGH | KEEP |
| **CAN-04-SUB1** | Refinement Notes Drawer | Candidate | SUBVIEW | Dedicated panel for natural-language instructions (e.g., 'Exclude C#') | CAN-04 | CAN-04 | Brain, Use Case | HIGH | KEEP |
| **CAN-05** | Personal 3D Avatar Studio | Candidate | FULL SCREEN | Workspace for generating personal 3D avatar via Avaturn | CAN-01, Profile, CAN-06 | CAN-05-SUB1, CAN-05-SUB2, CAN-01 | Brain, Use Case, Flow C | HIGH | KEEP |
| **CAN-05-SUB1** | Avaturn Embedded Iframe | Candidate | SUBVIEW | Embedded free Avaturn experience: photo capture, validation, preview | CAN-05 | CAN-05-SUB2 | Brain, Flow C | HIGH | KEEP |
| **CAN-05-SUB2** | Avatar VRM Handoff & Preview | Candidate | SUBVIEW | RoleCue conversion confirmation and VRM preview in candidate library | CAN-05-SUB1 | CAN-05, CAN-06 | Brain, Flow C | HIGH | KEEP |
| **CAN-06** | Configure Interview Session | Candidate | FULL SCREEN | Single composite setup: 3D interviewer, voice profile, room, difficulty, time | CAN-04, CAN-02, CAN-01 | CAN-07, CAN-05 | Brain, Route, Design | HIGH | KEEP |
| **CAN-07** | Test Audio & Interview Readiness| Candidate | FULL SCREEN | Verify microphone input, audio playback, and system rendering | CAN-06 (Target JD), CAN-13 (Job Posting) | CAN-08, CAN-06, CAN-12 | Brain, Route, Design | HIGH | KEEP |
| **CAN-07-SUB1** | Readiness Device Error Dialog | Candidate | SUBVIEW | Troubleshooting guidance for microphone or hardware permission failure | CAN-07 | CAN-07 | Brain, Design | HIGH | KEEP |
| **CAN-08** | Live 3D Interview Room | Candidate | FULL SCREEN | Real-time speech simulation: 3D avatar lip-sync, STT/TTS, question loop | CAN-07 | CAN-08-SUB1, CAN-08-SUB2, CAN-08-SUB3, CAN-09, CAN-13-SUB1 | Brain, Route, Design | HIGH | KEEP |
| **CAN-08-SUB1** | Interview Paused / Terminate Modal| Candidate | SUBVIEW | Pause live session; confirm resume or early exit | CAN-08 | CAN-08, CAN-01 | Brain, Design | HIGH | KEEP |
| **CAN-08-SUB2** | 2D Waveform Fallback View | Candidate | SUBVIEW | Performance-resilient 2D audio waveform when WebGL is unavailable | CAN-08 | CAN-08 | Brain, Design | HIGH | KEEP |
| **CAN-08-SUB3** | Evaluation Processing State | Candidate | SUBVIEW | Intermediate status screen while evaluation engine grades session | CAN-08 | CAN-09, CAN-13-SUB1 | Brain, Design | HIGH | KEEP |
| **CAN-09** | Interview Performance Report | Candidate | FULL SCREEN | Detailed diagnostic report: 0–100 score, 5-competency radar chart | CAN-08, CAN-10 | CAN-09-SUB1, CAN-09-SUB2, CAN-09-SUB3, CAN-06, CAN-10 | Brain, Route, Design | HIGH | KEEP |
| **CAN-09-SUB1** | Turn Critiques & Model Answers | Candidate | SUBVIEW | Detailed dialogue breakdown comparing candidate answers to model answers| CAN-09 | CAN-09 | Brain, Use Case | HIGH | KEEP |
| **CAN-09-SUB2** | Learning Roadmap & Recommendations| Candidate | SUBVIEW | Personalized study topics, official doc links, and practice tasks | CAN-09 | CAN-09 | Brain, Use Case | HIGH | KEEP |
| **CAN-09-SUB3** | Export Results Modal | Candidate | SUBVIEW | Options to export/download performance evaluation report (e.g., PDF) | CAN-09 | CAN-09 | Brain, Use Case | HIGH | KEEP |
| **CAN-10** | Interview History | Candidate | FULL SCREEN | Filterable log of past practice sessions, scores, and target roles | CAN-01, Navigation | CAN-09, CAN-06 | Brain, Route, Design | HIGH | KEEP |
| **CAN-11** | Job Board (Browse Postings) | Candidate | FULL SCREEN | Search and filter approved recruiter Job Postings by tech and role | CAN-01, Navigation | CAN-12 | Brain, Use Case, Flow B | HIGH | KEEP |
| **CAN-12** | Job Posting Detail | Candidate | FULL SCREEN | View employer requirements, locked company 3D interviewer & voice profile | CAN-11 | CAN-13, CAN-11 | Brain, Use Case, Flow B | HIGH | KEEP |
| **CAN-13** | Job Application & CV Upload | Candidate | FULL SCREEN | Upload CV/resume and initiate required locked technical interview | CAN-12 | CAN-07, CAN-12 | Brain, Use Case, Flow B | HIGH | KEEP |
| **CAN-13-SUB1** | Application Submitted Confirmation| Candidate | SUBVIEW | Confirmation that interview result and CV are submitted to Recruiter | CAN-08 | CAN-14 | Brain, Flow B | HIGH | KEEP |
| **CAN-14** | My Applications Tracking | Candidate | FULL SCREEN | Track submitted applications and recruiter decisions (Pending/Approved/Rejected)| CAN-01, Navigation | CAN-14-SUB1, CAN-11 | Brain, Use Case, Flow B | HIGH | KEEP |
| **CAN-14-SUB1** | Application Detail & Result View | Candidate | SUBVIEW | View submitted application details and associated Interview Result | CAN-14 | CAN-14 | Brain, Use Case | HIGH | KEEP |
| **CAN-15** | Membership & Billing | Candidate | FULL SCREEN | View active subscription status, renewal date, and upgrade options | CAN-01, Navigation | CAN-15-SUB1, CAN-15-SUB2, CAN-15-SUB3 | Brain, Route | HIGH | KEEP |
| **CAN-15-SUB1** | External Payment Gateway Redirect | Candidate | SUBVIEW | External handoff to payment gateway (VNPay/MoMo/Stripe) | CAN-15 | CAN-15 | Brain, Flow | HIGH | KEEP |
| **CAN-15-SUB2** | Unsubscribe Confirmation Dialog | Candidate | SUBVIEW | Dialog to confirm cancellation of active membership subscription | CAN-15 | CAN-15 | Brain, Use Case | HIGH | KEEP |
| **CAN-15-SUB3** | Payment Transaction History | Candidate | SUBVIEW | Ledger of past membership payment attempts, statuses, and receipts | CAN-15 | CAN-15 | Brain, Use Case | HIGH | KEEP |
| **REC-01** | Recruiter Dashboard | Recruiter | FULL SCREEN | Recruiter workspace hub: active postings, applicant counts, quick post | AUTH-02, Navigation | REC-02, REC-03, REC-05 | Brain, Use Case, Flow B | HIGH | KEEP |
| **REC-02** | My Job Postings Management | Recruiter | FULL SCREEN | Table of company postings with status (Pending Approval, Approved, etc.)| REC-01, Navigation | REC-03, REC-04, REC-02-SUB1, REC-05 | Brain, Use Case | HIGH | KEEP |
| **REC-02-SUB1** | Archive Job Posting Confirmation| Recruiter | SUBVIEW | Confirmation dialog to archive an active or filled job posting | REC-02, REC-04 | REC-02 | Brain, Use Case | HIGH | KEEP |
| **REC-03** | Create Job Posting | Recruiter | FULL SCREEN | Enter company JD requirements, seniority, technologies, and description | REC-01, REC-02 | REC-03-SUB1, REC-03-SUB2, REC-02 | Brain, Use Case, Flow B | HIGH | KEEP |
| **REC-03-SUB1** | AI Extraction Review & Edit | Recruiter | SUBVIEW | Optional AI-extracted competency review and confirmation | REC-03 | REC-03-SUB2 | Brain, Use Case | HIGH | KEEP |
| **REC-03-SUB2** | Configure Company 3D Interviewer| Recruiter | SUBVIEW | Select company 3D model and Voice Profile required for applicants | REC-03-SUB1 | REC-02 | Brain, Flow B | HIGH | KEEP |
| **REC-04** | Edit Job Posting | Recruiter | FULL SCREEN | Update existing job posting requirements and details | REC-02 | REC-02, REC-02-SUB1 | Brain, Use Case | HIGH | KEEP |
| **REC-05** | Received Applications List | Recruiter | FULL SCREEN | Filter candidate applications across own postings by status | REC-01, REC-02, Navigation | REC-06 | Brain, Use Case, Flow B | HIGH | KEEP |
| **REC-06** | Application Detail & Review | Recruiter | FULL SCREEN | View applicant profile, contact, CV/resume, and attached Interview Result| REC-05 | REC-06-SUB1, REC-06-SUB2, REC-05 | Brain, Use Case, Flow B | HIGH | KEEP |
| **REC-06-SUB1** | Attached Interview Result Viewer| Recruiter | SUBVIEW | View the candidate's Performance Report and turn transcript | REC-06 | REC-06 | Brain, Use Case | HIGH | KEEP |
| **REC-06-SUB2** | Application Adjudication Dialog | Recruiter | SUBVIEW | Definitive binary decision modal: record Approve or Reject | REC-06 | REC-06, REC-05 | Brain, Use Case, Flow B | HIGH | KEEP |
| **ADM-01** | Admin Governance Console | Administrator | FULL SCREEN | Supervisory overview: pending postings, active sessions, revenue metrics | AUTH-02, Navigation | ADM-02, ADM-04, ADM-06, ADM-08, ADM-09, ADM-10, ADM-11, ADM-13, ADM-14 | Brain, Route, Design | HIGH | KEEP |
| **ADM-02** | Account Governance (User List) | Administrator | FULL SCREEN | Search and filter platform user accounts (Candidate, Recruiter) | ADM-01, Navigation | ADM-03 | Brain, Route, Design | HIGH | KEEP |
| **ADM-03** | Account Detail & Lock/Unlock | Administrator | SUBVIEW | Inspect user profile history and execute security lock or unlock | ADM-02 | ADM-02 | Brain, Use Case | HIGH | KEEP |
| **ADM-04** | Job Posting Moderation Queue | Administrator | FULL SCREEN | Review queue of Recruiter Job Postings awaiting administrative approval | ADM-01, Navigation | ADM-05 | Brain, Use Case | HIGH | KEEP |
| **ADM-05** | Job Posting Review & Approval | Administrator | FULL SCREEN | Inspect submitted JD requirements, interviewer config, Approve/Reject | ADM-04 | ADM-05-SUB1, ADM-04 | Brain, Use Case | HIGH | KEEP |
| **ADM-05-SUB1** | Moderation Decision Dialog | Administrator | SUBVIEW | Confirmation dialog to record approval or rejection with reason note | ADM-05 | ADM-04 | Brain, Use Case | HIGH | KEEP |
| **ADM-06** | Interview Sessions Oversight | Administrator | FULL SCREEN | Search and filter interview sessions across platform by status and error | ADM-01, Navigation | ADM-07 | Brain, Route, Design | HIGH | KEEP |
| **ADM-07** | Interview Session Detail | Administrator | FULL SCREEN | Operational diagnostic view of session metadata, parameters, and turns | ADM-06 | ADM-06 | Brain, Route, Design | HIGH | KEEP |
| **ADM-08** | Interview Feature Configuration | Administrator | FULL SCREEN | Configure system-wide interview parameters, toggles, and limits | ADM-01, Navigation | ADM-01 | Brain, Use Case | HIGH | KEEP |
| **ADM-09** | AI Behaviour Management | Administrator | FULL SCREEN | Calibrate AI system prompts, Question guidance, and conversation rules | ADM-01, Navigation | ADM-09-SUB1, ADM-01 | Brain, Use Case | HIGH | KEEP |
| **ADM-09-SUB1** | Prompt Template Editor Drawer | Administrator | SUBVIEW | In-place editor for conversational AI prompt templates | ADM-09 | ADM-09 | Brain, Use Case | HIGH | KEEP |
| **ADM-10** | Evaluation Criteria Calibration | Administrator | FULL SCREEN | Calibrate 5 Core Competency rubrics, scoring templates, and weights | ADM-01, Navigation | ADM-10-SUB1, ADM-01 | Brain, Use Case | HIGH | KEEP |
| **ADM-10-SUB1** | Rubric Template Editor Drawer | Administrator | SUBVIEW | In-place editor for technical competency rubrics and benchmarks | ADM-10 | ADM-10 | Brain, Use Case | HIGH | KEEP |
| **ADM-11** | Voice Profile Catalog | Administrator | FULL SCREEN | Manage TTS voice profiles (languages, accents, providers, genders) | ADM-01, Navigation | ADM-11-SUB1, ADM-12 | Brain, Route, Design | HIGH | KEEP |
| **ADM-11-SUB1** | Delete Voice Profile Dialog | Administrator | SUBVIEW | Confirmation dialog to deactivate or delete obsolete voice profile | ADM-11 | ADM-11 | Brain, Use Case | HIGH | KEEP |
| **ADM-12** | Fetch Voice Profiles Modal | Administrator | SUBVIEW | Integration interface to query external TTS providers and import voices | ADM-11 | ADM-11 | Brain, Use Case | HIGH | KEEP |
| **ADM-13** | Payment Transactions Ledger | Administrator | FULL SCREEN | Immutable financial audit ledger of all membership checkout transactions | ADM-01, Navigation | ADM-14, ADM-15 | Brain, Route, Design | HIGH | KEEP |
| **ADM-14** | Revenue Report Generator | Administrator | FULL SCREEN | Generate and export aggregated financial revenue metrics by period | ADM-01, ADM-13, Navigation | ADM-13 | Brain, Use Case | HIGH | KEEP |
| **ADM-15** | Update Membership Price Modal | Administrator | SUBVIEW | Modal dialog to set new active monetary subscription price | ADM-01, ADM-13, ADM-14 | ADM-13 | Brain, Use Case | HIGH | KEEP |

---

## 6. Navigation Topology Specifications

This section defines the four standalone Navigation Graphs ready for visual layout in FigJam.

---

### Flow A: Public & Authentication Navigation Flow

#### Flow Metadata
- **Flow ID:** `FLOW-PUB-AUTH`
- **Flow Name:** Public & Authentication Navigation Flow
- **Primary Role:** Guest & Registered User
- **Entry Screen:** `PUB-01` (Public Landing Page)
- **Primary Exit / Destination Screens:**
  - `CAN-01` (Candidate Dashboard upon Candidate login)
  - `REC-01` (Recruiter Dashboard upon Recruiter login)
  - `ADM-01` (Admin Governance Console upon Admin login)

#### Nodes

| Node ID | Screen ID | Label | Type | Group | Importance |
|:---|:---|:---|:---|:---|:---|
| `N-PUB-01` | `PUB-01` | Public Landing Page | PAGE | Public | PRIMARY |
| `N-AUTH-01`| `AUTH-01` | Account Registration | PAGE | Authentication | PRIMARY |
| `N-AUTH-01S`| `AUTH-01-SUB1`| Email Verification Notice | SUBVIEW | Authentication | SUPPORTING |
| `N-AUTH-02`| `AUTH-02` | Account Login | PAGE | Authentication | PRIMARY |
| `N-AUTH-03`| `AUTH-03` | Forgot Password Request | PAGE | Authentication | SECONDARY |
| `N-AUTH-03S`| `AUTH-03-SUB1`| Reset Link Sent Notice | SUBVIEW | Authentication | SUPPORTING |
| `N-AUTH-04`| `AUTH-04` | Set New Password | PAGE | Authentication | SECONDARY |

#### Edges

| Edge ID | From | To | Trigger / Navigation Meaning | Direction | Notes |
|:---|:---|:---|:---|:---|:---|
| `E-PA-01` | `N-PUB-01` | `N-AUTH-01` | Click 'Get Started' / 'Register' | FORWARD | Directs to registration form |
| `E-PA-02` | `N-PUB-01` | `N-AUTH-02` | Click 'Sign In' / 'Log In' | FORWARD | Directs to login form |
| `E-PA-03` | `N-AUTH-01`| `N-AUTH-01S`| Submit valid registration | FORWARD | Displays verification instructions |
| `E-PA-04` | `N-AUTH-01S`| `N-AUTH-02` | Click 'Return to Login' / Verify link | FORWARD | Moves to login once verified |
| `E-PA-05` | `N-AUTH-01`| `N-AUTH-02` | Click 'Already have an account? Sign in'| BRANCH | Alternate route |
| `E-PA-06` | `N-AUTH-02`| `N-AUTH-01` | Click 'Need an account? Register' | BRANCH | Alternate route |
| `E-PA-07` | `N-AUTH-02`| `N-AUTH-03` | Click 'Forgot password?' | FORWARD | Enters password recovery |
| `E-PA-08` | `N-AUTH-03`| `N-AUTH-03S`| Submit recovery email request | FORWARD | Shows link dispatched feedback |
| `E-PA-09` | `N-AUTH-03`| `N-AUTH-02` | Click 'Back to Login' | BACK | Returns without resetting |
| `E-PA-10` | `N-AUTH-04`| `N-AUTH-02` | Submit new password successfully | FORWARD | Returns to login with new credentials |

#### Suggested Visual Grouping
```text
Public & Authentication Cluster
├── Public Acquisition (N-PUB-01)
│     └── [Get Started] ──> Registration Branch
│     └── [Sign In] ──────> Login Branch
├── Registration Branch (N-AUTH-01 ──> N-AUTH-01S)
├── Core Login Hub (N-AUTH-02) ──> [Role Gate Routing]
└── Password Recovery Branch (N-AUTH-03 ──> N-AUTH-03S ──> Email Token ──> N-AUTH-04)
```

---

### Flow B: Candidate Navigation Flow

#### Flow Metadata
- **Flow ID:** `FLOW-CANDIDATE`
- **Flow Name:** Candidate Navigation Flow
- **Primary Role:** Candidate
- **Entry Screen:** `CAN-01` (Candidate Dashboard)
- **Primary Exit / Destination Screens:**
  - `CAN-09` (Interview Performance Report)
  - `CAN-14` (My Applications Tracking)
  - `CAN-01` (Dashboard Return)

#### Nodes

| Node ID | Screen ID | Label | Type | Group | Importance |
|:---|:---|:---|:---|:---|:---|
| `N-CAN-01` | `CAN-01` | Candidate Dashboard | PAGE | Dashboard | PRIMARY |
| `N-CAN-02` | `CAN-02` | Target JD Library | PAGE | Target JD | SECONDARY |
| `N-CAN-03` | `CAN-03` | Add Target JD | PAGE | Target JD | PRIMARY |
| `N-CAN-03S`| `CAN-03-SUB1`| AI Extraction In-Progress | SUBVIEW | Target JD | SUPPORTING |
| `N-CAN-04` | `CAN-04` | Review & Refine Extracted JD | PAGE | Target JD | PRIMARY |
| `N-CAN-04S`| `CAN-04-SUB1`| Refinement Notes Drawer | SUBVIEW | Target JD | SECONDARY |
| `N-CAN-05` | `CAN-05` | Personal 3D Avatar Studio | PAGE | Avatar Studio | SECONDARY |
| `N-CAN-05S1`| `CAN-05-SUB1`| Avaturn Embedded Iframe | SUBVIEW | Avatar Studio | PRIMARY |
| `N-CAN-05S2`| `CAN-05-SUB2`| Avatar VRM Handoff & Preview | SUBVIEW | Avatar Studio | SECONDARY |
| `N-CAN-06` | `CAN-06` | Configure Interview Session | PAGE | Interview Setup | PRIMARY |
| `N-CAN-07` | `CAN-07` | Test Audio & Interview Readiness | PAGE | Simulation | PRIMARY |
| `N-CAN-07S`| `CAN-07-SUB1`| Readiness Device Error Dialog | SUBVIEW | Simulation | SUPPORTING |
| `N-CAN-08` | `CAN-08` | Live 3D Interview Room | PAGE | Simulation | PRIMARY |
| `N-CAN-08S1`| `CAN-08-SUB1`| Interview Paused / Exit Modal | SUBVIEW | Simulation | SUPPORTING |
| `N-CAN-08S2`| `CAN-08-SUB2`| 2D Waveform Fallback View | SUBVIEW | Simulation | SUPPORTING |
| `N-CAN-08S3`| `CAN-08-SUB3`| Evaluation Processing State | SUBVIEW | Simulation | SUPPORTING |
| `N-CAN-09` | `CAN-09` | Interview Performance Report | PAGE | Evaluation | PRIMARY |
| `N-CAN-09S1`| `CAN-09-SUB1`| Turn Critiques & Model Answers | SUBVIEW | Evaluation | SECONDARY |
| `N-CAN-09S2`| `CAN-09-SUB2`| Learning Roadmap & Recommendations| SUBVIEW | Evaluation | SECONDARY |
| `N-CAN-09S3`| `CAN-09-SUB3`| Export Results Modal | SUBVIEW | Evaluation | SUPPORTING |
| `N-CAN-10` | `CAN-10` | Interview History | PAGE | History | SECONDARY |
| `N-CAN-11` | `CAN-11` | Job Board (Browse Postings) | PAGE | Job Board | PRIMARY |
| `N-CAN-12` | `CAN-12` | Job Posting Detail | PAGE | Job Board | PRIMARY |
| `N-CAN-13` | `CAN-13` | Job Application & CV Upload | PAGE | Applications | PRIMARY |
| `N-CAN-13S`| `CAN-13-SUB1`| Application Submitted Confirmation| SUBVIEW | Applications | PRIMARY |
| `N-CAN-14` | `CAN-14` | My Applications Tracking | PAGE | Applications | SECONDARY |
| `N-CAN-14S`| `CAN-14-SUB1`| Application Detail & Result View | SUBVIEW | Applications | SUPPORTING |
| `N-CAN-15` | `CAN-15` | Membership & Billing | PAGE | Membership | SECONDARY |
| `N-CAN-15S1`| `CAN-15-SUB1`| Payment Gateway Redirect | SUBVIEW | Membership | SUPPORTING |
| `N-CAN-15S2`| `CAN-15-SUB2`| Unsubscribe Confirmation Dialog | SUBVIEW | Membership | SUPPORTING |
| `N-CAN-15S3`| `CAN-15-SUB3`| Payment Transaction History | SUBVIEW | Membership | SUPPORTING |
| `N-SH-01`  | `SHARED-01`| User Profile | PAGE | Account | SECONDARY |
| `N-SH-02`  | `SHARED-02`| Account Security & Password | PAGE | Account | SECONDARY |
| `N-SH-02S1`| `SHARED-02-SUB1`| Change Password Modal | SUBVIEW | Account | SUPPORTING |
| `N-SH-02S2`| `SHARED-02-SUB2`| Two-Factor Auth Setup Modal | SUBVIEW | Account | SUPPORTING |

#### Edges

| Edge ID | From | To | Trigger / Navigation Meaning | Direction | Notes |
|:---|:---|:---|:---|:---|:---|
| `E-CAN-01`| `N-CAN-01` | `N-CAN-03` | Click 'New Practice' / 'Upload JD' | FORWARD | Starts practice pipeline |
| `E-CAN-02`| `N-CAN-01` | `N-CAN-02` | Click 'Target JD Library' | FORWARD | Opens saved JDs |
| `E-CAN-03`| `N-CAN-01` | `N-CAN-10` | Click 'Interview History' | FORWARD | Opens past interviews |
| `E-CAN-04`| `N-CAN-01` | `N-CAN-11` | Click 'Explore Job Board' | FORWARD | Opens job discovery |
| `E-CAN-05`| `N-CAN-01` | `N-CAN-14` | Click 'My Applications' | FORWARD | Checks application statuses |
| `E-CAN-06`| `N-CAN-01` | `N-CAN-15` | Click 'Membership & Billing' | FORWARD | Checks subscription |
| `E-CAN-07`| `N-CAN-01` | `N-CAN-05` | Click '3D Avatar Studio' | FORWARD | Enters personal avatar creation |
| `E-CAN-08`| `N-CAN-02` | `N-CAN-03` | Click 'Add New JD' | FORWARD | Initiates new JD ingestion |
| `E-CAN-09`| `N-CAN-02` | `N-CAN-04` | Select existing Target JD | FORWARD | Reopens requirement review |
| `E-CAN-10`| `N-CAN-03` | `N-CAN-03S`| Submit text paste or PDF file | FORWARD | Shows extraction in-progress |
| `E-CAN-11`| `N-CAN-03S`| `N-CAN-04` | Extraction completes successfully | FORWARD | Opens extracted competency review |
| `E-CAN-12`| `N-CAN-04` | `N-CAN-04S`| Click 'Add Refinement Notes' | BRANCH | Opens notes drawer |
| `E-CAN-13`| `N-CAN-04S`| `N-CAN-04` | Save refinement notes | RETURN | Updates confirmed requirement set |
| `E-CAN-14`| `N-CAN-04` | `N-CAN-06` | Click 'Approve JD & Configure Interview'| FORWARD | Enters composite configuration |
| `E-CAN-15`| `N-CAN-05` | `N-CAN-05S1`| Click 'Create New Personal Avatar' | FORWARD | Launches embedded Avaturn iframe |
| `E-CAN-16`| `N-CAN-05S1`| `N-CAN-05S2`| Avaturn finishes GLB generation | FORWARD | RoleCue converts GLB to VRM |
| `E-CAN-17`| `N-CAN-05S2`| `N-CAN-05` | Save avatar to profile library | RETURN | Displays newly saved VRM avatar |
| `E-CAN-18`| `N-CAN-06` | `N-CAN-05` | Click 'Create Avatar' from interviewer picker | BRANCH | Alternate entry to avatar studio |
| `E-CAN-19`| `N-CAN-06` | `N-CAN-07` | Click 'Confirm & Test Readiness' | FORWARD | System compiles blueprint internally |
| `E-CAN-20`| `N-CAN-07` | `N-CAN-07S`| Audio/Mic test fails or denied | BRANCH | Shows hardware error guidance |
| `E-CAN-21`| `N-CAN-07S`| `N-CAN-07` | Retry device check | RETURN | Re-evaluates readiness |
| `E-CAN-22`| `N-CAN-07` | `N-CAN-08` | Click 'Enter Live Interview' | FORWARD | Starts 3D WebGL simulation |
| `E-CAN-23`| `N-CAN-08` | `N-CAN-08S1`| Click 'Pause Interview' | BRANCH | Opens pause/terminate dialog |
| `E-CAN-24`| `N-CAN-08S1`| `N-CAN-08` | Click 'Resume' | RETURN | Resumes active turn dialogue |
| `E-CAN-25`| `N-CAN-08S1`| `N-CAN-01` | Click 'End Session Early' | RETURN | Terminates session; returns to home |
| `E-CAN-26`| `N-CAN-08` | `N-CAN-08S2`| WebGL context lost / slow device | BRANCH | Seamless 2D waveform display |
| `E-CAN-27`| `N-CAN-08` | `N-CAN-08S3`| Final Question answered / Conclude | FORWARD | Bundles turns into evaluation |
| `E-CAN-28`| `N-CAN-08S3`| `N-CAN-09` | Evaluation completes (Target JD) | FORWARD | Opens comprehensive performance report |
| `E-CAN-29`| `N-CAN-09` | `N-CAN-09S1`| Click 'Turn Critiques & Answers' | BRANCH | Opens dialogue critiques tab/drawer |
| `E-CAN-30`| `N-CAN-09` | `N-CAN-09S2`| Click 'Learning Roadmap' | BRANCH | Opens personalized study roadmap |
| `E-CAN-31`| `N-CAN-09` | `N-CAN-09S3`| Click 'Export Report' | BRANCH | Opens export options modal |
| `E-CAN-32`| `N-CAN-09` | `N-CAN-06` | Click 'Repeat Practice with this JD'| FORWARD | Launches new configuration |
| `E-CAN-33`| `N-CAN-09` | `N-CAN-10` | Click 'View All History' | FORWARD | Navigates to history list |
| `E-CAN-34`| `N-CAN-10` | `N-CAN-09` | Select past session row | FORWARD | Reopens immutable performance report |
| `E-CAN-35`| `N-CAN-11` | `N-CAN-12` | Click Job Posting card | FORWARD | Opens Job Posting detail |
| `E-CAN-36`| `N-CAN-12` | `N-CAN-13` | Click 'Apply Now' | FORWARD | Enters application submission flow |
| `E-CAN-37`| `N-CAN-13` | `N-CAN-07` | Upload CV & Click 'Start Required Interview' | FORWARD | Locks company 3D interviewer & voice |
| `E-CAN-38`| `N-CAN-08S3`| `N-CAN-13S`| Evaluation completes (Job Posting)| FORWARD | Submits Application with Interview Result |
| `E-CAN-39`| `N-CAN-13S`| `N-CAN-14` | Click 'Track My Application' | FORWARD | Opens application tracking list |
| `E-CAN-40`| `N-CAN-14` | `N-CAN-14S`| Click application row | BRANCH | Views application details & attached result |
| `E-CAN-41`| `N-CAN-15` | `N-CAN-15S1`| Click 'Subscribe' / 'Upgrade Plan' | FORWARD | Redirects to external payment gateway |
| `E-CAN-42`| `N-CAN-15S1`| `N-CAN-15` | Gateway webhook verified & redirect | RETURN | Shows active membership confirmation |
| `E-CAN-43`| `N-CAN-15` | `N-CAN-15S2`| Click 'Cancel Subscription' | BRANCH | Opens unsubscribe confirmation modal |
| `E-CAN-44`| `N-CAN-15S2`| `N-CAN-15` | Confirm cancellation | RETURN | Updates status to Cancelled |
| `E-CAN-45`| `N-CAN-15` | `N-CAN-15S3`| Click 'Transaction History' | BRANCH | Opens transaction ledger tab |
| `E-CAN-46`| `N-CAN-01` | `N-SH-01`  | Click Profile avatar in top bar | FORWARD | Opens user profile |
| `E-CAN-47`| `N-SH-01`  | `N-SH-02`  | Click 'Security & Password' tab | FORWARD | Opens account security |
| `E-CAN-48`| `N-SH-02`  | `N-SH-02S1`| Click 'Change Password' | BRANCH | Opens change password modal |
| `E-CAN-49`| `N-SH-02`  | `N-SH-02S2`| Click 'Enable 2FA' | BRANCH | Opens 2FA QR code modal |

#### Suggested Visual Grouping
```text
Candidate Workspace (N-CAN-01 Dashboard Hub)
├── Practice & Assessment Pipeline
│     ├── JD Ingestion: N-CAN-02 (Library) <──> N-CAN-03 (Add JD) ──> N-CAN-03S ──> N-CAN-04 (Review & Refine) [N-CAN-04S]
│     ├── Interview Setup: N-CAN-06 (Composite Configuration)
│     │     └── [System compiles hidden blueprint internally]
│     ├── Simulation: N-CAN-07 (Readiness) [N-CAN-07S] ──> N-CAN-08 (Live 3D Room) [N-CAN-08S1, S2, S3]
│     └── Evaluation & History: N-CAN-09 (Report) [N-CAN-09S1, S2, S3] <──> N-CAN-10 (History)
├── Personal 3D Avatar Studio
│     └── N-CAN-05 (Studio) ──> N-CAN-05S1 (Avaturn Embed) ──> N-CAN-05S2 (VRM Handoff)
├── Job Board & Career Opportunities
│     └── N-CAN-11 (Job Board) ──> N-CAN-12 (Posting Detail) ──> N-CAN-13 (Apply & CV)
│           └── [Takes locked interview: N-CAN-07 ──> N-CAN-08]
│           └── N-CAN-13S (Submitted) ──> N-CAN-14 (Tracking) [N-CAN-14S]
└── Membership & Account Management
      ├── Membership: N-CAN-15 (Billing) [N-CAN-15S1 Gateway, N-CAN-15S2 Unsubscribe, N-CAN-15S3 Ledger]
      └── Profile & Security: N-SH-01 (Profile) ──> N-SH-02 (Security) [N-SH-02S1, N-SH-02S2]
```

---

### Flow C: Recruiter Navigation Flow

#### Flow Metadata
- **Flow ID:** `FLOW-RECRUITER`
- **Flow Name:** Recruiter Navigation Flow
- **Primary Role:** Recruiter
- **Entry Screen:** `REC-01` (Recruiter Dashboard)
- **Primary Exit / Destination Screens:**
  - `REC-02` (My Job Postings Management)
  - `REC-06` (Application Detail & Binary Adjudication)

#### Nodes

| Node ID | Screen ID | Label | Type | Group | Importance |
|:---|:---|:---|:---|:---|:---|
| `N-REC-01` | `REC-01` | Recruiter Dashboard | PAGE | Dashboard | PRIMARY |
| `N-REC-02` | `REC-02` | My Job Postings Management | PAGE | Postings | PRIMARY |
| `N-REC-02S`| `REC-02-SUB1`| Archive Job Posting Confirmation| SUBVIEW | Postings | SUPPORTING |
| `N-REC-03` | `REC-03` | Create Job Posting | PAGE | Postings | PRIMARY |
| `N-REC-03S1`| `REC-03-SUB1`| AI Extraction Review & Edit | SUBVIEW | Postings | SECONDARY |
| `N-REC-03S2`| `REC-03-SUB2`| Configure Company 3D Interviewer| SUBVIEW | Postings | PRIMARY |
| `N-REC-04` | `REC-04` | Edit Job Posting | PAGE | Postings | SECONDARY |
| `N-REC-05` | `REC-05` | Received Applications List | PAGE | Applications | PRIMARY |
| `N-REC-06` | `REC-06` | Application Detail & Review | PAGE | Applications | PRIMARY |
| `N-REC-06S1`| `REC-06-SUB1`| Attached Interview Result Viewer| SUBVIEW | Applications | PRIMARY |
| `N-REC-06S2`| `REC-06-SUB2`| Application Adjudication Dialog | SUBVIEW | Applications | PRIMARY |
| `N-SH-01`  | `SHARED-01`| User Profile | PAGE | Account | SECONDARY |
| `N-SH-02`  | `SHARED-02`| Account Security & Password | PAGE | Account | SECONDARY |
| `N-SH-02S1`| `SHARED-02-SUB1`| Change Password Modal | SUBVIEW | Account | SUPPORTING |
| `N-SH-02S2`| `SHARED-02-SUB2`| Two-Factor Auth Setup Modal | SUBVIEW | Account | SUPPORTING |

#### Edges

| Edge ID | From | To | Trigger / Navigation Meaning | Direction | Notes |
|:---|:---|:---|:---|:---|:---|
| `E-REC-01`| `N-REC-01` | `N-REC-03` | Click 'Create Job Posting' | FORWARD | Begins new posting flow |
| `E-REC-02`| `N-REC-01` | `N-REC-02` | Click 'Manage Job Postings' | FORWARD | Opens postings list |
| `E-REC-03`| `N-REC-01` | `N-REC-05` | Click 'Review Applications' | FORWARD | Opens incoming applications |
| `E-REC-04`| `N-REC-02` | `N-REC-03` | Click 'New Job Posting' | FORWARD | Starts creation form |
| `E-REC-05`| `N-REC-02` | `N-REC-04` | Click 'Edit' on existing posting | FORWARD | Modifies posting content |
| `E-REC-06`| `N-REC-02` | `N-REC-02S`| Click 'Archive' on posting | BRANCH | Opens archive confirmation dialog |
| `E-REC-07`| `N-REC-02S`| `N-REC-02` | Confirm archive action | RETURN | Marks posting ARCHIVED |
| `E-REC-08`| `N-REC-02` | `N-REC-05` | Click 'View Applications' for posting| FORWARD | Filters applications by posting ID |
| `E-REC-09`| `N-REC-03` | `N-REC-03S1`| Provide JD content & click 'Extract'| FORWARD | Opens structured skill review |
| `E-REC-10`| `N-REC-03S1`| `N-REC-03S2`| Confirm skills & click 'Next' | FORWARD | Selects company 3D model & voice |
| `E-REC-11`| `N-REC-03S2`| `N-REC-02` | Click 'Submit for Admin Approval' | FORWARD | Sets status PENDING_APPROVAL |
| `E-REC-12`| `N-REC-04` | `N-REC-02` | Save posting updates | RETURN | Returns to postings management |
| `E-REC-13`| `N-REC-05` | `N-REC-06` | Click candidate application row | FORWARD | Opens detailed applicant dossier |
| `E-REC-14`| `N-REC-06` | `N-REC-06S1`| Click 'View Interview Evaluation' | BRANCH | Opens attached Interview Result |
| `E-REC-15`| `N-REC-06` | `N-REC-06S2`| Click 'Approve' or 'Reject' | BRANCH | Opens definitive decision modal |
| `E-REC-16`| `N-REC-06S2`| `N-REC-05` | Confirm decision (Approved/Rejected)| RETURN | Status finalized; returns to list |
| `E-REC-17`| `N-REC-01` | `N-SH-01`  | Click Profile avatar in top bar | FORWARD | Opens recruiter profile |
| `E-REC-18`| `N-SH-01`  | `N-SH-02`  | Click 'Security & Password' tab | FORWARD | Opens account security |
| `E-REC-19`| `N-SH-02`  | `N-SH-02S1`| Click 'Change Password' | BRANCH | Opens change password modal |
| `E-REC-20`| `N-SH-02`  | `N-SH-02S2`| Click 'Enable 2FA' | BRANCH | Opens 2FA QR code modal |

#### Suggested Visual Grouping
```text
Recruiter Workspace (N-REC-01 Dashboard Hub)
├── Job Posting Lifecycle
│     ├── N-REC-02 (Postings Management) [N-REC-02S Archive Modal]
│     ├── N-REC-03 (Create Posting) ──> N-REC-03S1 (AI Extract) ──> N-REC-03S2 (Company Interviewer Config)
│     └── N-REC-04 (Edit Posting)
├── Candidate Application Review & Decision (Scope Termination)
│     ├── N-REC-05 (Applications List)
│     └── N-REC-06 (Application Detail)
│           ├── N-REC-06S1 (Attached Interview Result Viewer)
│           └── N-REC-06S2 (Approve / Reject Binary Adjudication)
└── Profile & Security
      └── N-SH-01 (Profile) ──> N-SH-02 (Security) [N-SH-02S1, N-SH-02S2]
```

---

### Flow D: Administrator Navigation Flow

#### Flow Metadata
- **Flow ID:** `FLOW-ADMIN`
- **Flow Name:** Administrator Navigation Flow
- **Primary Role:** Administrator
- **Entry Screen:** `ADM-01` (Admin Governance Console)
- **Primary Exit / Destination Screens:**
  - `ADM-02` (Account Governance)
  - `ADM-04` (Job Posting Moderation Queue)
  - `ADM-06` (Interview Sessions Oversight)
  - `ADM-11` (Voice Profile Catalog)
  - `ADM-13` (Payment Transactions Ledger)

#### Nodes

| Node ID | Screen ID | Label | Type | Group | Importance |
|:---|:---|:---|:---|:---|:---|
| `N-ADM-01` | `ADM-01` | Admin Governance Console | PAGE | Governance | PRIMARY |
| `N-ADM-02` | `ADM-02` | Account Governance (User List) | PAGE | Accounts | PRIMARY |
| `N-ADM-03` | `ADM-03` | Account Detail & Lock/Unlock | SUBVIEW | Accounts | PRIMARY |
| `N-ADM-04` | `ADM-04` | Job Posting Moderation Queue | PAGE | Moderation | PRIMARY |
| `N-ADM-05` | `ADM-05` | Job Posting Review & Approval | PAGE | Moderation | PRIMARY |
| `N-ADM-05S`| `ADM-05-SUB1`| Moderation Decision Dialog | SUBVIEW | Moderation | PRIMARY |
| `N-ADM-06` | `ADM-06` | Interview Sessions Oversight | PAGE | Sessions | PRIMARY |
| `N-ADM-07` | `ADM-07` | Interview Session Detail | PAGE | Sessions | SECONDARY |
| `N-ADM-08` | `ADM-08` | Interview Feature Configuration | PAGE | Configuration | SECONDARY |
| `N-ADM-09` | `ADM-09` | AI Behaviour Management | PAGE | Configuration | PRIMARY |
| `N-ADM-09S`| `ADM-09-SUB1`| Prompt Template Editor Drawer | SUBVIEW | Configuration | SECONDARY |
| `N-ADM-10` | `ADM-10` | Evaluation Criteria Calibration | PAGE | Calibration | PRIMARY |
| `N-ADM-10S`| `ADM-10-SUB1`| Rubric Template Editor Drawer | SUBVIEW | Calibration | SECONDARY |
| `N-ADM-11` | `ADM-11` | Voice Profile Catalog | PAGE | Voice Profiles | PRIMARY |
| `N-ADM-11S`| `ADM-11-SUB1`| Delete Voice Profile Dialog | SUBVIEW | Voice Profiles | SUPPORTING |
| `N-ADM-12` | `ADM-12` | Fetch Voice Profiles Modal | SUBVIEW | Voice Profiles | PRIMARY |
| `N-ADM-13` | `ADM-13` | Payment Transactions Ledger | PAGE | Finance | PRIMARY |
| `N-ADM-14` | `ADM-14` | Revenue Report Generator | PAGE | Finance | PRIMARY |
| `N-ADM-15` | `ADM-15` | Update Membership Price Modal | SUBVIEW | Finance | PRIMARY |
| `N-SH-01`  | `SHARED-01`| User Profile | PAGE | Account | SECONDARY |

#### Edges

| Edge ID | From | To | Trigger / Navigation Meaning | Direction | Notes |
|:---|:---|:---|:---|:---|:---|
| `E-ADM-01`| `N-ADM-01` | `N-ADM-02` | Click 'User Accounts' | FORWARD | Opens user accounts governance |
| `E-ADM-02`| `N-ADM-01` | `N-ADM-04` | Click 'Job Posting Moderation' | FORWARD | Opens pending posting queue |
| `E-ADM-03`| `N-ADM-01` | `N-ADM-06` | Click 'Interview Sessions' | FORWARD | Opens session monitoring |
| `E-ADM-04`| `N-ADM-01` | `N-ADM-08` | Click 'Feature Configuration' | FORWARD | Opens runtime feature toggles |
| `E-ADM-05`| `N-ADM-01` | `N-ADM-09` | Click 'AI Behaviour' | FORWARD | Opens prompt tuning |
| `E-ADM-06`| `N-ADM-01` | `N-ADM-10` | Click 'Evaluation Rubrics' | FORWARD | Opens 5-competency rubrics |
| `E-ADM-07`| `N-ADM-01` | `N-ADM-11` | Click 'Voice Profiles' | FORWARD | Opens voice profile catalog |
| `E-ADM-08`| `N-ADM-01` | `N-ADM-13` | Click 'Payment Ledger' | FORWARD | Opens transaction audit ledger |
| `E-ADM-09`| `N-ADM-01` | `N-ADM-14` | Click 'Revenue Reports' | FORWARD | Opens financial reporting |
| `E-ADM-10`| `N-ADM-02` | `N-ADM-03` | Click user row | BRANCH | Opens account detail and lock/unlock |
| `E-ADM-11`| `N-ADM-03` | `N-ADM-02` | Confirm lock/unlock action | RETURN | Updates account status; records audit |
| `E-ADM-12`| `N-ADM-04` | `N-ADM-05` | Select pending job posting row | FORWARD | Enters moderation inspection view |
| `E-ADM-13`| `N-ADM-05` | `N-ADM-05S`| Click 'Approve' or 'Reject' | BRANCH | Opens moderation decision dialog |
| `E-ADM-14`| `N-ADM-05S`| `N-ADM-04` | Confirm approval/rejection | RETURN | Updates posting status; returns to queue |
| `E-ADM-15`| `N-ADM-06` | `N-ADM-07` | Click session row | FORWARD | Inspects operational diagnostics |
| `E-ADM-16`| `N-ADM-07` | `N-ADM-06` | Click 'Back to Sessions' | BACK | Returns to session list |
| `E-ADM-17`| `N-ADM-08` | `N-ADM-01` | Save configuration parameters | RETURN | Updates platform settings |
| `E-ADM-18`| `N-ADM-09` | `N-ADM-09S`| Select AI prompt template | BRANCH | Opens prompt editor drawer |
| `E-ADM-19`| `N-ADM-09S`| `N-ADM-09` | Save prompt version | RETURN | Commits updated AI prompt |
| `E-ADM-20`| `N-ADM-10` | `N-ADM-10S`| Select competency rubric | BRANCH | Opens rubric editor drawer |
| `E-ADM-21`| `N-ADM-10S`| `N-ADM-10` | Save rubric weights | RETURN | Commits calibrated rubrics |
| `E-ADM-22`| `N-ADM-11` | `N-ADM-12` | Click 'Fetch New Voices' | BRANCH | Opens provider voice import modal |
| `E-ADM-23`| `N-ADM-12` | `N-ADM-11` | Select provider voices & import | RETURN | Adds new profiles to catalog |
| `E-ADM-24`| `N-ADM-11` | `N-ADM-11S`| Click 'Delete' on obsolete voice | BRANCH | Opens delete confirmation dialog |
| `E-ADM-25`| `N-ADM-11S`| `N-ADM-11` | Confirm deletion | RETURN | Removes profile from catalog |
| `E-ADM-26`| `N-ADM-13` | `N-ADM-14` | Click 'Generate Revenue Report' | FORWARD | Moves to revenue aggregator |
| `E-ADM-27`| `N-ADM-13` | `N-ADM-15` | Click 'Update Membership Price' | BRANCH | Opens pricing update modal |
| `E-ADM-28`| `N-ADM-14` | `N-ADM-15` | Click 'Adjust Base Price' | BRANCH | Opens pricing update modal |
| `E-ADM-29`| `N-ADM-15` | `N-ADM-13` | Save updated price | RETURN | Persists new membership rate |
| `E-ADM-30`| `N-ADM-01` | `N-SH-01`  | Click Profile in navigation | FORWARD | Opens admin profile |

#### Suggested Visual Grouping
```text
Admin Governance Console (N-ADM-01 Core Hub)
├── User Security Governance
│     └── N-ADM-02 (User Accounts) ──> N-ADM-03 (Account Detail & Lock/Unlock)
├── Job Posting Content Moderation
│     └── N-ADM-04 (Moderation Queue) ──> N-ADM-05 (Posting Review) [N-ADM-05S Decision Dialog]
├── Interview Operations & Diagnostics
│     └── N-ADM-06 (Session Oversight) ──> N-ADM-07 (Session Detail & Diagnostics)
├── AI Calibration & System Toggles
│     ├── N-ADM-08 (Feature Configuration)
│     ├── N-ADM-09 (AI Behaviour Management) [N-ADM-09S Prompt Editor]
│     └── N-ADM-10 (Evaluation Rubrics Calibration) [N-ADM-10S Rubric Editor]
├── Voice Profile Management (TTS Provider Curation)
│     └── N-ADM-11 (Voice Catalog) [N-ADM-11S Delete Modal, N-ADM-12 Fetch Voices Modal]
└── Financial & Revenue Governance
      ├── N-ADM-13 (Payment Transactions Ledger) [N-ADM-15 Update Price Modal]
      └── N-ADM-14 (Revenue Report Generator)
```

---

## 7. Machine-Actionable FigJam Drawing Handoff

```yaml
# ==============================================================================
# FigJam Drawing Handoff Specification: RoleCue Navigation Flows
# ==============================================================================

# ------------------------------------------------------------------------------
# 1. Public & Authentication Navigation Flow
# ------------------------------------------------------------------------------
flow:
  id: FLOW-PUB-AUTH
  title: Public & Authentication Navigation Flow
  role: Guest & Registered User
  entry_node: N-PUB-01

groups:
  - id: G-PUB
    label: Public Acquisition
    node_ids: [N-PUB-01]
  - id: G-AUTH
    label: Authentication & Password Recovery
    node_ids: [N-AUTH-01, N-AUTH-01S, N-AUTH-02, N-AUTH-03, N-AUTH-03S, N-AUTH-04]

nodes:
  - id: N-PUB-01
    screen_id: PUB-01
    label: Public Landing Page
    type: PAGE
    group: G-PUB
    importance: PRIMARY
  - id: N-AUTH-01
    screen_id: AUTH-01
    label: Account Registration
    type: PAGE
    group: G-AUTH
    importance: PRIMARY
  - id: N-AUTH-01S
    screen_id: AUTH-01-SUB1
    label: Email Verification Notice
    type: SUBVIEW
    group: G-AUTH
    importance: SUPPORTING
  - id: N-AUTH-02
    screen_id: AUTH-02
    label: Account Login
    type: PAGE
    group: G-AUTH
    importance: PRIMARY
  - id: N-AUTH-03
    screen_id: AUTH-03
    label: Forgot Password Request
    type: PAGE
    group: G-AUTH
    importance: SECONDARY
  - id: N-AUTH-03S
    screen_id: AUTH-03-SUB1
    label: Reset Link Sent Notice
    type: SUBVIEW
    group: G-AUTH
    importance: SUPPORTING
  - id: N-AUTH-04
    screen_id: AUTH-04
    label: Set New Password
    type: PAGE
    group: G-AUTH
    importance: SECONDARY

edges:
  - id: E-PA-01
    from: N-PUB-01
    to: N-AUTH-01
    direction: FORWARD
    label: Click Register
  - id: E-PA-02
    from: N-PUB-01
    to: N-AUTH-02
    direction: FORWARD
    label: Click Sign In
  - id: E-PA-03
    from: N-AUTH-01
    to: N-AUTH-01S
    direction: FORWARD
    label: Submit Registration
  - id: E-PA-04
    from: N-AUTH-01S
    to: N-AUTH-02
    direction: FORWARD
    label: Verified / Back to Login
  - id: E-PA-05
    from: N-AUTH-01
    to: N-AUTH-02
    direction: BRANCH
    label: Has Account
  - id: E-PA-06
    from: N-AUTH-02
    to: N-AUTH-01
    direction: BRANCH
    label: Need Account
  - id: E-PA-07
    from: N-AUTH-02
    to: N-AUTH-03
    direction: FORWARD
    label: Forgot Password
  - id: E-PA-08
    from: N-AUTH-03
    to: N-AUTH-03S
    direction: FORWARD
    label: Send Reset Email
  - id: E-PA-09
    from: N-AUTH-03
    to: N-AUTH-02
    direction: BACK
    label: Cancel
  - id: E-PA-10
    from: N-AUTH-04
    to: N-AUTH-02
    direction: FORWARD
    label: Password Updated

# ------------------------------------------------------------------------------
# 2. Candidate Navigation Flow
# ------------------------------------------------------------------------------
flow:
  id: FLOW-CANDIDATE
  title: Candidate Navigation Flow
  role: Candidate
  entry_node: N-CAN-01

groups:
  - id: G-CAN-DASH
    label: Dashboard Hub
    node_ids: [N-CAN-01]
  - id: G-CAN-JD
    label: Target JD Ingestion & Refinement
    node_ids: [N-CAN-02, N-CAN-03, N-CAN-03S, N-CAN-04, N-CAN-04S]
  - id: G-CAN-AVATAR
    label: Personal 3D Avatar Studio
    node_ids: [N-CAN-05, N-CAN-05S1, N-CAN-05S2]
  - id: G-CAN-SIM
    label: Interview Setup & Simulation
    node_ids: [N-CAN-06, N-CAN-07, N-CAN-07S, N-CAN-08, N-CAN-08S1, N-CAN-08S2, N-CAN-08S3]
  - id: G-CAN-EVAL
    label: Evaluation & History
    node_ids: [N-CAN-09, N-CAN-09S1, N-CAN-09S2, N-CAN-09S3, N-CAN-10]
  - id: G-CAN-JOBS
    label: Job Board & Applications
    node_ids: [N-CAN-11, N-CAN-12, N-CAN-13, N-CAN-13S, N-CAN-14, N-CAN-14S]
  - id: G-CAN-BILLING
    label: Membership & Billing
    node_ids: [N-CAN-15, N-CAN-15S1, N-CAN-15S2, N-CAN-15S3]
  - id: G-CAN-ACCOUNT
    label: Account & Security
    node_ids: [N-SH-01, N-SH-02, N-SH-02S1, N-SH-02S2]

nodes:
  - id: N-CAN-01
    screen_id: CAN-01
    label: Candidate Dashboard
    type: PAGE
    group: G-CAN-DASH
    importance: PRIMARY
  - id: N-CAN-02
    screen_id: CAN-02
    label: Target JD Library
    type: PAGE
    group: G-CAN-JD
    importance: SECONDARY
  - id: N-CAN-03
    screen_id: CAN-03
    label: Add Target JD
    type: PAGE
    group: G-CAN-JD
    importance: PRIMARY
  - id: N-CAN-03S
    screen_id: CAN-03-SUB1
    label: AI Extraction In-Progress
    type: SUBVIEW
    group: G-CAN-JD
    importance: SUPPORTING
  - id: N-CAN-04
    screen_id: CAN-04
    label: Review & Refine Extracted JD
    type: PAGE
    group: G-CAN-JD
    importance: PRIMARY
  - id: N-CAN-04S
    screen_id: CAN-04-SUB1
    label: Refinement Notes Drawer
    type: SUBVIEW
    group: G-CAN-JD
    importance: SECONDARY
  - id: N-CAN-05
    screen_id: CAN-05
    label: Personal 3D Avatar Studio
    type: PAGE
    group: G-CAN-AVATAR
    importance: SECONDARY
  - id: N-CAN-05S1
    screen_id: CAN-05-SUB1
    label: Avaturn Embedded Iframe
    type: SUBVIEW
    group: G-CAN-AVATAR
    importance: PRIMARY
  - id: N-CAN-05S2
    screen_id: CAN-05-SUB2
    label: Avatar VRM Handoff & Preview
    type: SUBVIEW
    group: G-CAN-AVATAR
    importance: SECONDARY
  - id: N-CAN-06
    screen_id: CAN-06
    label: Configure Interview Session
    type: PAGE
    group: G-CAN-SIM
    importance: PRIMARY
  - id: N-CAN-07
    screen_id: CAN-07
    label: Test Audio & Interview Readiness
    type: PAGE
    group: G-CAN-SIM
    importance: PRIMARY
  - id: N-CAN-07S
    screen_id: CAN-07-SUB1
    label: Readiness Device Error Dialog
    type: SUBVIEW
    group: G-CAN-SIM
    importance: SUPPORTING
  - id: N-CAN-08
    screen_id: CAN-08
    label: Live 3D Interview Room
    type: PAGE
    group: G-CAN-SIM
    importance: PRIMARY
  - id: N-CAN-08S1
    screen_id: CAN-08-SUB1
    label: Interview Paused / Exit Modal
    type: SUBVIEW
    group: G-CAN-SIM
    importance: SUPPORTING
  - id: N-CAN-08S2
    screen_id: CAN-08-SUB2
    label: 2D Waveform Fallback View
    type: SUBVIEW
    group: G-CAN-SIM
    importance: SUPPORTING
  - id: N-CAN-08S3
    screen_id: CAN-08-SUB3
    label: Evaluation Processing State
    type: SUBVIEW
    group: G-CAN-SIM
    importance: SUPPORTING
  - id: N-CAN-09
    screen_id: CAN-09
    label: Interview Performance Report
    type: PAGE
    group: G-CAN-EVAL
    importance: PRIMARY
  - id: N-CAN-09S1
    screen_id: CAN-09-SUB1
    label: Turn Critiques & Model Answers
    type: SUBVIEW
    group: G-CAN-EVAL
    importance: SECONDARY
  - id: N-CAN-09S2
    screen_id: CAN-09-SUB2
    label: Learning Roadmap & Recommendations
    type: SUBVIEW
    group: G-CAN-EVAL
    importance: SECONDARY
  - id: N-CAN-09S3
    screen_id: CAN-09-SUB3
    label: Export Results Modal
    type: SUBVIEW
    group: G-CAN-EVAL
    importance: SUPPORTING
  - id: N-CAN-10
    screen_id: CAN-10
    label: Interview History
    type: PAGE
    group: G-CAN-EVAL
    importance: SECONDARY
  - id: N-CAN-11
    screen_id: CAN-11
    label: Job Board (Browse Postings)
    type: PAGE
    group: G-CAN-JOBS
    importance: PRIMARY
  - id: N-CAN-12
    screen_id: CAN-12
    label: Job Posting Detail
    type: PAGE
    group: G-CAN-JOBS
    importance: PRIMARY
  - id: N-CAN-13
    screen_id: CAN-13
    label: Job Application & CV Upload
    type: PAGE
    group: G-CAN-JOBS
    importance: PRIMARY
  - id: N-CAN-13S
    screen_id: CAN-13-SUB1
    label: Application Submitted Confirmation
    type: SUBVIEW
    group: G-CAN-JOBS
    importance: PRIMARY
  - id: N-CAN-14
    screen_id: CAN-14
    label: My Applications Tracking
    type: PAGE
    group: G-CAN-JOBS
    importance: SECONDARY
  - id: N-CAN-14S
    screen_id: CAN-14-SUB1
    label: Application Detail & Result View
    type: SUBVIEW
    group: G-CAN-JOBS
    importance: SUPPORTING
  - id: N-CAN-15
    screen_id: CAN-15
    label: Membership & Billing
    type: PAGE
    group: G-CAN-BILLING
    importance: SECONDARY
  - id: N-CAN-15S1
    screen_id: CAN-15-SUB1
    label: Payment Gateway Redirect
    type: SUBVIEW
    group: G-CAN-BILLING
    importance: SUPPORTING
  - id: N-CAN-15S2
    screen_id: CAN-15-SUB2
    label: Unsubscribe Confirmation Dialog
    type: SUBVIEW
    group: G-CAN-BILLING
    importance: SUPPORTING
  - id: N-CAN-15S3
    screen_id: CAN-15-SUB3
    label: Payment Transaction History
    type: SUBVIEW
    group: G-CAN-BILLING
    importance: SUPPORTING
  - id: N-SH-01
    screen_id: SHARED-01
    label: User Profile
    type: PAGE
    group: G-CAN-ACCOUNT
    importance: SECONDARY
  - id: N-SH-02
    screen_id: SHARED-02
    label: Account Security & Password
    type: PAGE
    group: G-CAN-ACCOUNT
    importance: SECONDARY
  - id: N-SH-02S1
    screen_id: SHARED-02-SUB1
    label: Change Password Modal
    type: SUBVIEW
    group: G-CAN-ACCOUNT
    importance: SUPPORTING
  - id: N-SH-02S2
    screen_id: SHARED-02-SUB2
    label: Two-Factor Auth Setup Modal
    type: SUBVIEW
    group: G-CAN-ACCOUNT
    importance: SUPPORTING

edges:
  - id: E-CAN-01
    from: N-CAN-01
    to: N-CAN-03
    direction: FORWARD
    label: Start Practice
  - id: E-CAN-02
    from: N-CAN-01
    to: N-CAN-02
    direction: FORWARD
    label: View JD Library
  - id: E-CAN-03
    from: N-CAN-01
    to: N-CAN-10
    direction: FORWARD
    label: View History
  - id: E-CAN-04
    from: N-CAN-01
    to: N-CAN-11
    direction: FORWARD
    label: Explore Jobs
  - id: E-CAN-05
    from: N-CAN-01
    to: N-CAN-14
    direction: FORWARD
    label: View Applications
  - id: E-CAN-06
    from: N-CAN-01
    to: N-CAN-15
    direction: FORWARD
    label: Manage Membership
  - id: E-CAN-07
    from: N-CAN-01
    to: N-CAN-05
    direction: FORWARD
    label: Personal Avatar
  - id: E-CAN-08
    from: N-CAN-02
    to: N-CAN-03
    direction: FORWARD
    label: Add New JD
  - id: E-CAN-09
    from: N-CAN-02
    to: N-CAN-04
    direction: FORWARD
    label: Select Saved JD
  - id: E-CAN-10
    from: N-CAN-03
    to: N-CAN-03S
    direction: FORWARD
    label: Upload / Extract
  - id: E-CAN-11
    from: N-CAN-03S
    to: N-CAN-04
    direction: FORWARD
    label: Extraction Complete
  - id: E-CAN-12
    from: N-CAN-04
    to: N-CAN-04S
    direction: BRANCH
    label: Open Notes
  - id: E-CAN-13
    from: N-CAN-04S
    to: N-CAN-04
    direction: RETURN
    label: Save Notes
  - id: E-CAN-14
    from: N-CAN-04
    to: N-CAN-06
    direction: FORWARD
    label: Approve & Configure
  - id: E-CAN-15
    from: N-CAN-05
    to: N-CAN-05S1
    direction: FORWARD
    label: Open Avaturn
  - id: E-CAN-16
    from: N-CAN-05S1
    to: N-CAN-05S2
    direction: FORWARD
    label: GLB to VRM
  - id: E-CAN-17
    from: N-CAN-05S2
    to: N-CAN-05
    direction: RETURN
    label: Save Avatar
  - id: E-CAN-18
    from: N-CAN-06
    to: N-CAN-05
    direction: BRANCH
    label: Create Avatar
  - id: E-CAN-19
    from: N-CAN-06
    to: N-CAN-07
    direction: FORWARD
    label: Test Readiness
  - id: E-CAN-20
    from: N-CAN-07
    to: N-CAN-07S
    direction: BRANCH
    label: Device Error
  - id: E-CAN-21
    from: N-CAN-07S
    to: N-CAN-07
    direction: RETURN
    label: Retry
  - id: E-CAN-22
    from: N-CAN-07
    to: N-CAN-08
    direction: FORWARD
    label: Start Simulation
  - id: E-CAN-23
    from: N-CAN-08
    to: N-CAN-08S1
    direction: BRANCH
    label: Pause
  - id: E-CAN-24
    from: N-CAN-08S1
    to: N-CAN-08
    direction: RETURN
    label: Resume
  - id: E-CAN-25
    from: N-CAN-08S1
    to: N-CAN-01
    direction: RETURN
    label: Exit Early
  - id: E-CAN-26
    from: N-CAN-08
    to: N-CAN-08S2
    direction: BRANCH
    label: Degrade to 2D
  - id: E-CAN-27
    from: N-CAN-08
    to: N-CAN-08S3
    direction: FORWARD
    label: Finish Interview
  - id: E-CAN-28
    from: N-CAN-08S3
    to: N-CAN-09
    direction: FORWARD
    label: Report Ready
  - id: E-CAN-29
    from: N-CAN-09
    to: N-CAN-09S1
    direction: BRANCH
    label: View Critiques
  - id: E-CAN-30
    from: N-CAN-09
    to: N-CAN-09S2
    direction: BRANCH
    label: View Roadmap
  - id: E-CAN-31
    from: N-CAN-09
    to: N-CAN-09S3
    direction: BRANCH
    label: Export
  - id: E-CAN-32
    from: N-CAN-09
    to: N-CAN-06
    direction: FORWARD
    label: Repeat Practice
  - id: E-CAN-33
    from: N-CAN-09
    to: N-CAN-10
    direction: FORWARD
    label: All History
  - id: E-CAN-34
    from: N-CAN-10
    to: N-CAN-09
    direction: FORWARD
    label: Reopen Report
  - id: E-CAN-35
    from: N-CAN-11
    to: N-CAN-12
    direction: FORWARD
    label: View Job
  - id: E-CAN-36
    from: N-CAN-12
    to: N-CAN-13
    direction: FORWARD
    label: Apply
  - id: E-CAN-37
    from: N-CAN-13
    to: N-CAN-07
    direction: FORWARD
    label: Start Job Interview
  - id: E-CAN-38
    from: N-CAN-08S3
    to: N-CAN-13S
    direction: FORWARD
    label: Submit Application
  - id: E-CAN-39
    from: N-CAN-13S
    to: N-CAN-14
    direction: FORWARD
    label: View Applications
  - id: E-CAN-40
    from: N-CAN-14
    to: N-CAN-14S
    direction: BRANCH
    label: View Result
  - id: E-CAN-41
    from: N-CAN-15
    to: N-CAN-15S1
    direction: FORWARD
    label: Checkout Gateway
  - id: E-CAN-42
    from: N-CAN-15S1
    to: N-CAN-15
    direction: RETURN
    label: Payment Complete
  - id: E-CAN-43
    from: N-CAN-15
    to: N-CAN-15S2
    direction: BRANCH
    label: Unsubscribe
  - id: E-CAN-44
    from: N-CAN-15S2
    to: N-CAN-15
    direction: RETURN
    label: Cancelled
  - id: E-CAN-45
    from: N-CAN-15
    to: N-CAN-15S3
    direction: BRANCH
    label: View Ledger
  - id: E-CAN-46
    from: N-CAN-01
    to: N-SH-01
    direction: FORWARD
    label: Open Profile
  - id: E-CAN-47
    from: N-SH-01
    to: N-SH-02
    direction: FORWARD
    label: Open Security
  - id: E-CAN-48
    from: N-SH-02
    to: N-SH-02S1
    direction: BRANCH
    label: Change Password
  - id: E-CAN-49
    from: N-SH-02
    to: N-SH-02S2
    direction: BRANCH
    label: Enable 2FA

# ------------------------------------------------------------------------------
# 3. Recruiter Navigation Flow
# ------------------------------------------------------------------------------
flow:
  id: FLOW-RECRUITER
  title: Recruiter Navigation Flow
  role: Recruiter
  entry_node: N-REC-01

groups:
  - id: G-REC-DASH
    label: Dashboard Hub
    node_ids: [N-REC-01]
  - id: G-REC-POSTINGS
    label: Job Posting Lifecycle
    node_ids: [N-REC-02, N-REC-02S, N-REC-03, N-REC-03S1, N-REC-03S2, N-REC-04]
  - id: G-REC-APPS
    label: Application Review & Decision
    node_ids: [N-REC-05, N-REC-06, N-REC-06S1, N-REC-06S2]
  - id: G-REC-ACCOUNT
    label: Account & Security
    node_ids: [N-SH-01, N-SH-02, N-SH-02S1, N-SH-02S2]

nodes:
  - id: N-REC-01
    screen_id: REC-01
    label: Recruiter Dashboard
    type: PAGE
    group: G-REC-DASH
    importance: PRIMARY
  - id: N-REC-02
    screen_id: REC-02
    label: My Job Postings Management
    type: PAGE
    group: G-REC-POSTINGS
    importance: PRIMARY
  - id: N-REC-02S
    screen_id: REC-02-SUB1
    label: Archive Job Posting Confirmation
    type: SUBVIEW
    group: G-REC-POSTINGS
    importance: SUPPORTING
  - id: N-REC-03
    screen_id: REC-03
    label: Create Job Posting
    type: PAGE
    group: G-REC-POSTINGS
    importance: PRIMARY
  - id: N-REC-03S1
    screen_id: REC-03-SUB1
    label: AI Extraction Review & Edit
    type: SUBVIEW
    group: G-REC-POSTINGS
    importance: SECONDARY
  - id: N-REC-03S2
    screen_id: REC-03-SUB2
    label: Configure Company 3D Interviewer
    type: SUBVIEW
    group: G-REC-POSTINGS
    importance: PRIMARY
  - id: N-REC-04
    screen_id: REC-04
    label: Edit Job Posting
    type: PAGE
    group: G-REC-POSTINGS
    importance: SECONDARY
  - id: N-REC-05
    screen_id: REC-05
    label: Received Applications List
    type: PAGE
    group: G-REC-APPS
    importance: PRIMARY
  - id: N-REC-06
    screen_id: REC-06
    label: Application Detail & Review
    type: PAGE
    group: G-REC-APPS
    importance: PRIMARY
  - id: N-REC-06S1
    screen_id: REC-06-SUB1
    label: Attached Interview Result Viewer
    type: SUBVIEW
    group: G-REC-APPS
    importance: PRIMARY
  - id: N-REC-06S2
    screen_id: REC-06-SUB2
    label: Application Adjudication Dialog
    type: SUBVIEW
    group: G-REC-APPS
    importance: PRIMARY
  - id: N-SH-01
    screen_id: SHARED-01
    label: User Profile
    type: PAGE
    group: G-REC-ACCOUNT
    importance: SECONDARY
  - id: N-SH-02
    screen_id: SHARED-02
    label: Account Security & Password
    type: PAGE
    group: G-REC-ACCOUNT
    importance: SECONDARY
  - id: N-SH-02S1
    screen_id: SHARED-02-SUB1
    label: Change Password Modal
    type: SUBVIEW
    group: G-REC-ACCOUNT
    importance: SUPPORTING
  - id: N-SH-02S2
    screen_id: SHARED-02-SUB2
    label: Two-Factor Auth Setup Modal
    type: SUBVIEW
    group: G-REC-ACCOUNT
    importance: SUPPORTING

edges:
  - id: E-REC-01
    from: N-REC-01
    to: N-REC-03
    direction: FORWARD
    label: Create Posting
  - id: E-REC-02
    from: N-REC-01
    to: N-REC-02
    direction: FORWARD
    label: Manage Postings
  - id: E-REC-03
    from: N-REC-01
    to: N-REC-05
    direction: FORWARD
    label: View Applications
  - id: E-REC-04
    from: N-REC-02
    to: N-REC-03
    direction: FORWARD
    label: New Posting
  - id: E-REC-05
    from: N-REC-02
    to: N-REC-04
    direction: FORWARD
    label: Edit Posting
  - id: E-REC-06
    from: N-REC-02
    to: N-REC-02S
    direction: BRANCH
    label: Archive
  - id: E-REC-07
    from: N-REC-02S
    to: N-REC-02
    direction: RETURN
    label: Confirmed Archive
  - id: E-REC-08
    from: N-REC-02
    to: N-REC-05
    direction: FORWARD
    label: Filter Applications
  - id: E-REC-09
    from: N-REC-03
    to: N-REC-03S1
    direction: FORWARD
    label: Extract Skills
  - id: E-REC-10
    from: N-REC-03S1
    to: N-REC-03S2
    direction: FORWARD
    label: Select Model & Voice
  - id: E-REC-11
    from: N-REC-03S2
    to: N-REC-02
    direction: FORWARD
    label: Submit for Approval
  - id: E-REC-12
    from: N-REC-04
    to: N-REC-02
    direction: RETURN
    label: Save Updates
  - id: E-REC-13
    from: N-REC-05
    to: N-REC-06
    direction: FORWARD
    label: Inspect Applicant
  - id: E-REC-14
    from: N-REC-06
    to: N-REC-06S1
    direction: BRANCH
    label: View Evaluation
  - id: E-REC-15
    from: N-REC-06
    to: N-REC-06S2
    direction: BRANCH
    label: Adjudicate
  - id: E-REC-16
    from: N-REC-06S2
    to: N-REC-05
    direction: RETURN
    label: Decision Finalized
  - id: E-REC-17
    from: N-REC-01
    to: N-SH-01
    direction: FORWARD
    label: Open Profile
  - id: E-REC-18
    from: N-SH-01
    to: N-SH-02
    direction: FORWARD
    label: Open Security
  - id: E-REC-19
    from: N-SH-02
    to: N-SH-02S1
    direction: BRANCH
    label: Change Password
  - id: E-REC-20
    from: N-SH-02
    to: N-SH-02S2
    direction: BRANCH
    label: Enable 2FA

# ------------------------------------------------------------------------------
# 4. Administrator Navigation Flow
# ------------------------------------------------------------------------------
flow:
  id: FLOW-ADMIN
  title: Administrator Navigation Flow
  role: Administrator
  entry_node: N-ADM-01

groups:
  - id: G-ADM-GOV
    label: Governance Console
    node_ids: [N-ADM-01]
  - id: G-ADM-ACCOUNTS
    label: Account Security Governance
    node_ids: [N-ADM-02, N-ADM-03]
  - id: G-ADM-MOD
    label: Job Posting Moderation
    node_ids: [N-ADM-04, N-ADM-05, N-ADM-05S]
  - id: G-ADM-SESSIONS
    label: Session Oversight & Diagnostics
    node_ids: [N-ADM-06, N-ADM-07]
  - id: G-ADM-CALIB
    label: AI & Evaluation Calibration
    node_ids: [N-ADM-08, N-ADM-09, N-ADM-09S, N-ADM-10, N-ADM-10S]
  - id: G-ADM-VOICE
    label: Voice Profile Management
    node_ids: [N-ADM-11, N-ADM-11S, N-ADM-12]
  - id: G-ADM-FINANCE
    label: Financial & Revenue Governance
    node_ids: [N-ADM-13, N-ADM-14, N-ADM-15]
  - id: G-ADM-ACCOUNT
    label: Administrator Profile
    node_ids: [N-SH-01]

nodes:
  - id: N-ADM-01
    screen_id: ADM-01
    label: Admin Governance Console
    type: PAGE
    group: G-ADM-GOV
    importance: PRIMARY
  - id: N-ADM-02
    screen_id: ADM-02
    label: Account Governance (User List)
    type: PAGE
    group: G-ADM-ACCOUNTS
    importance: PRIMARY
  - id: N-ADM-03
    screen_id: ADM-03
    label: Account Detail & Lock/Unlock
    type: SUBVIEW
    group: G-ADM-ACCOUNTS
    importance: PRIMARY
  - id: N-ADM-04
    screen_id: ADM-04
    label: Job Posting Moderation Queue
    type: PAGE
    group: G-ADM-MOD
    importance: PRIMARY
  - id: N-ADM-05
    screen_id: ADM-05
    label: Job Posting Review & Approval
    type: PAGE
    group: G-ADM-MOD
    importance: PRIMARY
  - id: N-ADM-05S
    screen_id: ADM-05-SUB1
    label: Moderation Decision Dialog
    type: SUBVIEW
    group: G-ADM-MOD
    importance: PRIMARY
  - id: N-ADM-06
    screen_id: ADM-06
    label: Interview Sessions Oversight
    type: PAGE
    group: G-ADM-SESSIONS
    importance: PRIMARY
  - id: N-ADM-07
    screen_id: ADM-07
    label: Interview Session Detail
    type: PAGE
    group: G-ADM-SESSIONS
    importance: SECONDARY
  - id: N-ADM-08
    screen_id: ADM-08
    label: Interview Feature Configuration
    type: PAGE
    group: G-ADM-CALIB
    importance: SECONDARY
  - id: N-ADM-09
    screen_id: ADM-09
    label: AI Behaviour Management
    type: PAGE
    group: G-ADM-CALIB
    importance: PRIMARY
  - id: N-ADM-09S
    screen_id: ADM-09-SUB1
    label: Prompt Template Editor Drawer
    type: SUBVIEW
    group: G-ADM-CALIB
    importance: SECONDARY
  - id: N-ADM-10
    screen_id: ADM-10
    label: Evaluation Criteria Calibration
    type: PAGE
    group: G-ADM-CALIB
    importance: PRIMARY
  - id: N-ADM-10S
    screen_id: ADM-10-SUB1
    label: Rubric Template Editor Drawer
    type: SUBVIEW
    group: G-ADM-CALIB
    importance: SECONDARY
  - id: N-ADM-11
    screen_id: ADM-11
    label: Voice Profile Catalog
    type: PAGE
    group: G-ADM-VOICE
    importance: PRIMARY
  - id: N-ADM-11S
    screen_id: ADM-11-SUB1
    label: Delete Voice Profile Dialog
    type: SUBVIEW
    group: G-ADM-VOICE
    importance: SUPPORTING
  - id: N-ADM-12
    screen_id: ADM-12
    label: Fetch Voice Profiles Modal
    type: SUBVIEW
    group: G-ADM-VOICE
    importance: PRIMARY
  - id: N-ADM-13
    screen_id: ADM-13
    label: Payment Transactions Ledger
    type: PAGE
    group: G-ADM-FINANCE
    importance: PRIMARY
  - id: N-ADM-14
    screen_id: ADM-14
    label: Revenue Report Generator
    type: PAGE
    group: G-ADM-FINANCE
    importance: PRIMARY
  - id: N-ADM-15
    screen_id: ADM-15
    label: Update Membership Price Modal
    type: SUBVIEW
    group: G-ADM-FINANCE
    importance: PRIMARY
  - id: N-SH-01
    screen_id: SHARED-01
    label: User Profile
    type: PAGE
    group: G-ADM-ACCOUNT
    importance: SECONDARY

edges:
  - id: E-ADM-01
    from: N-ADM-01
    to: N-ADM-02
    direction: FORWARD
    label: Manage Accounts
  - id: E-ADM-02
    from: N-ADM-01
    to: N-ADM-04
    direction: FORWARD
    label: Review Postings
  - id: E-ADM-03
    from: N-ADM-01
    to: N-ADM-06
    direction: FORWARD
    label: Monitor Sessions
  - id: E-ADM-04
    from: N-ADM-01
    to: N-ADM-08
    direction: FORWARD
    label: Feature Toggles
  - id: E-ADM-05
    from: N-ADM-01
    to: N-ADM-09
    direction: FORWARD
    label: AI Behaviour
  - id: E-ADM-06
    from: N-ADM-01
    to: N-ADM-10
    direction: FORWARD
    label: Rubric Tuning
  - id: E-ADM-07
    from: N-ADM-01
    to: N-ADM-11
    direction: FORWARD
    label: Voice Profiles
  - id: E-ADM-08
    from: N-ADM-01
    to: N-ADM-13
    direction: FORWARD
    label: Audit Payments
  - id: E-ADM-09
    from: N-ADM-01
    to: N-ADM-14
    direction: FORWARD
    label: Revenue Reports
  - id: E-ADM-10
    from: N-ADM-02
    to: N-ADM-03
    direction: BRANCH
    label: Inspect Account
  - id: E-ADM-11
    from: N-ADM-03
    to: N-ADM-02
    direction: RETURN
    label: Toggle Lock
  - id: E-ADM-12
    from: N-ADM-04
    to: N-ADM-05
    direction: FORWARD
    label: Inspect Posting
  - id: E-ADM-13
    from: N-ADM-05
    to: N-ADM-05S
    direction: BRANCH
    label: Record Decision
  - id: E-ADM-14
    from: N-ADM-05S
    to: N-ADM-04
    direction: RETURN
    label: Moderation Done
  - id: E-ADM-15
    from: N-ADM-06
    to: N-ADM-07
    direction: FORWARD
    label: Inspect Session
  - id: E-ADM-16
    from: N-ADM-07
    to: N-ADM-06
    direction: BACK
    label: Back to List
  - id: E-ADM-17
    from: N-ADM-08
    to: N-ADM-01
    direction: RETURN
    label: Settings Saved
  - id: E-ADM-18
    from: N-ADM-09
    to: N-ADM-09S
    direction: BRANCH
    label: Edit Template
  - id: E-ADM-19
    from: N-ADM-09S
    to: N-ADM-09
    direction: RETURN
    label: Save Prompt
  - id: E-ADM-20
    from: N-ADM-10
    to: N-ADM-10S
    direction: BRANCH
    label: Edit Rubric
  - id: E-ADM-21
    from: N-ADM-10S
    to: N-ADM-10
    direction: RETURN
    label: Save Rubric
  - id: E-ADM-22
    from: N-ADM-11
    to: N-ADM-12
    direction: BRANCH
    label: Fetch Voices
  - id: E-ADM-23
    from: N-ADM-12
    to: N-ADM-11
    direction: RETURN
    label: Import Voices
  - id: E-ADM-24
    from: N-ADM-11
    to: N-ADM-11S
    direction: BRANCH
    label: Delete Voice
  - id: E-ADM-25
    from: N-ADM-11S
    to: N-ADM-11
    direction: RETURN
    label: Voice Deleted
  - id: E-ADM-26
    from: N-ADM-13
    to: N-ADM-14
    direction: FORWARD
    label: View Revenue
  - id: E-ADM-27
    from: N-ADM-13
    to: N-ADM-15
    direction: BRANCH
    label: Update Price
  - id: E-ADM-28
    from: N-ADM-14
    to: N-ADM-15
    direction: BRANCH
    label: Adjust Price
  - id: E-ADM-29
    from: N-ADM-15
    to: N-ADM-13
    direction: RETURN
    label: Price Saved
  - id: E-ADM-30
    from: N-ADM-01
    to: N-SH-01
    direction: FORWARD
    label: Admin Profile
```

---

## 8. Stale / Conflicting Screen Evidence

During the audit of existing code and design repositories, the following screens, routes, and UI states were identified as conflicting with canonical contracts:

```text
1. Blueprint Preview & Confirmation Screen
   - Where found: 
     ai-interview-practice-design/SCREEN-INVENTORY.md (line 112)
     ai-interview-practice-design/flows/01-candidate-flow.png
     frontend/src/config/routes.ts (/interviews/new/blueprint)
   - Why it conflicts:
     Canonical Product Decisions explicitly establish: "The Interview Blueprint is strictly internal
     and hidden from the Candidate. Candidates never view, edit, or directly confirm an Interview Blueprint.
     There is no blueprint preview capability."
   - Recommended Action:
     REMOVE-STALE. Eliminate from user-facing navigation. Blueprint compilation is an internal backend event.

2. Admin 3D Avatar Catalog Management
   - Where found:
     ai-interview-practice-design/SCREEN-INVENTORY.md (lines 154-155)
     frontend/src/app/admin/avatars/page.tsx
     frontend/src/config/routes.ts (/admin/avatars)
   - Why it conflicts:
     Canonical Product Decisions explicitly state: "The Administrator manages the catalog of Voice Profiles
     sourced from TTS providers, but does NOT manage the 3D avatar catalog or 3D background scenes.
     3D avatars and environments are built-in presets."
   - Recommended Action:
     REMOVE-STALE. Remove route and navigation link. Admin only curates TTS Voice Profiles.

3. Practice Credit Balances, Packages, & Deduction Screens
   - Where found:
     ai-interview-practice-design/SCREEN-INVENTORY.md (lines 87-88, 110, 136-137, 168)
     frontend/src/app/(candidate)/billing/page.tsx
   - Why it conflicts:
     Canonical Product Decisions state: "Platform monetization is structured exclusively around Candidate
     Membership Subscriptions and recorded Payment Transactions. Concepts such as candidate practice credit
     balances, credit packages, per-interview credit deductions... are explicitly removed from scope."
   - Recommended Action:
     REMOVE-STALE / REFACTOR. Replace credit purchase screens with Candidate Membership Subscription
     management (CAN-15) and immutable transaction history.

4. Public Marketing Pricing Page (/pricing)
   - Where found:
     frontend/src/app/(public)/pricing/page.tsx
     ai-interview-practice-design/SCREEN-INVENTORY.md (lines 87-88)
   - Why it conflicts:
     Canonical Decision 11: "Guest capabilities are strictly limited to viewing the landing page and
     registering for an account. Guests do NOT have access to pricing exploration, pricing plan viewing,
     public 3D avatar teasers, or anonymous demos."
   - Recommended Action:
     REMOVE-STALE as a public Guest screen. Subscription plans are visible to authenticated Candidates in CAN-15.

5. Reset Password as Independent Use Case
   - Where found:
     frontend/src/app/(auth)/reset-password/page.tsx
     ai-interview-practice-design/SCREEN-INVENTORY.md (line 94)
   - Why it conflicts:
     Canonical Auth README: "Forgot Password is the single password-recovery capability. It includes issuing
     and validating a recovery link or token and setting a replacement password; Reset Password is not a
     separate formal capability."
   - Recommended Action:
     KEEP as a secondary UI screen (AUTH-04) reached via token link, but document it as part of the
     Forgot Password user journey rather than an independent formal Use Case.

6. Missing Recruiter Role & Screens
   - Where found:
     Completely absent from ai-interview-practice-design and early frontend routes.
   - Why it conflicts:
     Recruiter is a ratified main business actor in the 57 Use Cases and Core Flows (Flow B).
   - Recommended Action:
     ADDED to inventory: REC-01 through REC-06 (Dashboard, Job Postings, Application Review, Binary Adjudication).

7. Missing Candidate Job Board & Job Application Screens
   - Where found:
     Absent from prior design inventory.
   - Why it conflicts:
     Ratified Flow B requires Candidate Job Board browsing, Job Posting Detail, Apply & CV Upload, and Application Tracking.
   - Recommended Action:
     ADDED to inventory: CAN-11 through CAN-14.

8. Missing Candidate Personal 3D Avatar Studio
   - Where found:
     Old Report 1 excluded custom avatars (EX-08).
   - Why it conflicts:
     Canonical Flow C and Decision 7 ratify the embedded free Avaturn iframe experience with VRM persistence.
   - Recommended Action:
     ADDED to inventory: CAN-05, CAN-05-SUB1, CAN-05-SUB2.
```

---

## 9. Human Decisions Required

The following unresolved design trade-offs materially affect navigation structure and screen counts. They are scoped strictly to navigation topology and layout ergonomics:

1. **Target JD Flow: Multi-Route Wizard vs. Integrated Stepper View**
   - *Option A (Multi-Route):* Separate pages for `/interviews/new/job-description` $\rightarrow$ `/interviews/new/skills` $\rightarrow$ `/interviews/new/setup`. (Matches prior route tree).
   - *Option B (Recommended - Integrated Stepper):* Consolidated `/interviews/new` workspace where raw JD paste/upload, skill review, refinement notes, and composite configuration execute as cohesive steps without full-page reloads.
   - *Navigation Impact:* Multi-route adds 3 full pages; integrated stepper consolidates them into 2 full pages with subview stages.

2. **Personal 3D Avatar Studio: Dedicated Route vs. Profile Modal**
   - *Option A (Recommended - Dedicated Route):* Full page `/avatar-studio` (`CAN-05`) embedding the Avaturn iframe. Provides maximum WebGL canvas viewport, clear photo capture webcam ergonomics, and distraction-free styling.
   - *Option B (Modal / Drawer):* In-page modal or drawer launched directly from User Profile or Interview Setup.
   - *Navigation Impact:* Dedicated route elevates 3D identity as a core platform pillar; modal keeps it secondary.

3. **Job Board Placement: Primary Global Navigation vs. Dashboard Sub-Section**
   - *Option A (Recommended - Primary Global Nav):* Dedicated sidebar item `/jobs` (`CAN-11`) parallel to `/interviews` and `/dashboard`.
   - *Option B (Dashboard Tab):* Secondary discovery widget inside the Candidate Dashboard.
   - *Navigation Impact:* Primary global nav emphasizes the bridge between interview practice and real job applications.

4. **Recruiter Application Dossier: Full Page vs. Split Master-Detail Pane**
   - *Option A (Recommended - Full Page):* Dedicated route `/recruiter/applications/[id]` (`REC-06`) to accommodate the applicant contact, resume PDF viewer, and multi-dimensional Interview Result (radar chart, turn transcripts, scores) side-by-side.
   - *Option B (Slide-Out Drawer):* Right-hand drawer within the applications table. May feel cramped given the complexity of the 5-competency evaluation report.
   - *Navigation Impact:* Full page improves evaluation legibility; drawer minimizes context switching.

---

## 10. Traceability Matrix to 57 Use Cases & Core Flows

| Core Product Domain | Formal Capability / Use Case Scope | Screen Mappings |
|:---|:---|:---|
| **Public & Acquisition** | View Landing Page, Register Account | `PUB-01`, `AUTH-01`, `AUTH-01-SUB1` |
| **Authentication & Identity**| Login, Logout, Forgot Password, Reset Token, Change Password, 2FA | `AUTH-02`, `AUTH-03`, `AUTH-03-SUB1`, `AUTH-04`, `SHARED-01`, `SHARED-02`, `SHARED-02-SUB1`, `SHARED-02-SUB2` |
| **Target JD & Refinement** | Ingest Target JD (text/PDF), AI extraction, tag review, natural-language notes | `CAN-02`, `CAN-03`, `CAN-03-SUB1`, `CAN-04`, `CAN-04-SUB1` |
| **Interview Configuration** | Composite setup (system/personal 3D avatar, TTS voice, 3D room, difficulty, duration) | `CAN-06` |
| **Simulation Runtime** | Audio readiness preflight, 3D WebGL dialogue, lip-sync, pause/resume, 2D fallback | `CAN-07`, `CAN-07-SUB1`, `CAN-08`, `CAN-08-SUB1`, `CAN-08-SUB2`, `CAN-08-SUB3` |
| **Evaluation & Reporting** | 5-competency radar, turn critiques, model answers, learning roadmap, export | `CAN-09`, `CAN-09-SUB1`, `CAN-09-SUB2`, `CAN-09-SUB3`, `CAN-10` |
| **Personal 3D Avatar** | Free Avaturn iframe embed, photo capture, preview, GLB-to-VRM conversion, library | `CAN-05`, `CAN-05-SUB1`, `CAN-05-SUB2` |
| **Job Board & Application** | Browse approved postings, posting detail, apply & CV upload, application tracking | `CAN-11`, `CAN-12`, `CAN-13`, `CAN-13-SUB1`, `CAN-14`, `CAN-14-SUB1` |
| **Membership & Payments** | Subscribe, cancel subscription, gateway redirect, audit ledger | `CAN-15`, `CAN-15-SUB1`, `CAN-15-SUB2`, `CAN-15-SUB3` |
| **Recruiter Job Postings** | Create posting, AI skill extraction, select company 3D model & voice, edit, archive | `REC-01`, `REC-02`, `REC-02-SUB1`, `REC-03`, `REC-03-SUB1`, `REC-03-SUB2`, `REC-04` |
| **Recruiter Applications** | Filter applications, review applicant & attached interview result, binary decision | `REC-05`, `REC-06`, `REC-06-SUB1`, `REC-06-SUB2` |
| **Admin Account Oversight** | Filter candidate/recruiter accounts, security lock & unlock | `ADM-01`, `ADM-02`, `ADM-03` |
| **Admin Content Moderation**| Moderate submitted recruiter job postings, approve/reject | `ADM-04`, `ADM-05`, `ADM-05-SUB1` |
| **Admin Session Diagnostics**| Search/filter interview sessions, inspect session parameters and errors | `ADM-06`, `ADM-07` |
| **Admin AI & Evaluation** | Feature toggles, AI prompt tuning, 5-competency rubric weights calibration | `ADM-08`, `ADM-09`, `ADM-09-SUB1`, `ADM-10`, `ADM-10-SUB1` |
| **Admin Voice Profiles** | Curate TTS voice catalog, fetch new profiles from TTS providers, delete profiles | `ADM-11`, `ADM-11-SUB1`, `ADM-12` |
| **Admin Financial Governance**| Audit transaction records, generate revenue reports, update membership price | `ADM-13`, `ADM-14`, `ADM-15` |
