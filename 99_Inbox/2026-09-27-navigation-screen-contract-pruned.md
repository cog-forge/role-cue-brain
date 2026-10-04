# RoleCue Screen Inventory Pruning & Report Contract Specification
**Document Version:** 2.0 (Pruned & Normalized Report Staging Contract)  
**Date:** 2026-09-27  
**Status:** FINAL STAGING CONTRACT / READY FOR REPORT 7 SECTION III & FIGJAM DRAWING  
**Scope Authority:** Canonical RoleCue Brain, Finalized 57 Use Cases, Finalized Main Business Flows, Ratified Product Decisions  
**Deliverable Target:** Report 7 Section III (3.1.1 Navigation Flows, 3.1.2 Screen Descriptions, 3.1.3 Screen Authorization, 3.1.4 Non-Screen Functions)

---

## 1. Executive Summary & Pruning Framework

This document represents **Phase 2** of the RoleCue navigation architecture pipeline. It reviews, prunes, reclassifies, and normalizes the findings from the initial research pass (`99_Inbox/2026-09-27-navigation-screen-inventory.md`) into an authoritative, contract-grade staging specification.

### 1.1. Pruning & Reclassification Taxonomy

To prevent diagram clutter while preserving complete system specifications, every interface state and system operation discovered in prior audits is classified into exactly one of four categories:

1. **`DRAW` (Navigation Flow Node):**
   - Major navigable screen destinations (`PAGE`) and meaningful user-opened subviews (`SUBVIEW`).
   - Appears as an interactive node in **3.1.1 Navigation Flows** and in the machine-actionable FigJam drawing handoff.
   - Also included in **3.1.2 Screen Descriptions** and **3.1.3 Screen Authorization**.
2. **`DOC-ONLY` (Descriptive Screen / Subview):**
   - Legitimate user-facing UI screens, secondary panels, drawers, or action modals.
   - Excluded from **3.1.1 Navigation Flows** to prevent visual noise.
   - Preserved in full detail in **3.1.2 Screen Descriptions** and **3.1.3 Screen Authorization**.
3. **`NON-SCREEN` (Internal System / Architectural Function):**
   - Automated backend processes, AI pipeline orchestration, streaming audio synchronization, and scheduled routines with no direct standalone user-facing screen.
   - Removed from screen inventories and formalized in **3.1.4 Non-Screen Functions**.
4. **`REMOVE` (Stale / Contradicted Item):**
   - Legacy concepts from obsolete drafts or prototypes contradicted by ratified product decisions (e.g., exposed Blueprint previews, Admin avatar catalogs, practice credit wallets, public guest pricing).
   - Fully eliminated from all Report 7 deliverables and recorded in the Pruning Change Log.

### 1.2. Ratified Product Invariants Applied

The four major human architectural decisions are officially ratified and enforced:
- **Target JD Flow:** Modeled as four logically distinct navigable screen states (`Target JD Library`, `Add Target JD`, `Review & Refine Extracted JD`, and `Configure Interview Session`) regardless of physical frontend router grouping.
- **Personal 3D Avatar:** Modeled as a dedicated full-screen `Personal 3D Avatar Studio` with the free Avaturn iframe embed as an internal workspace subview.
- **Job Board:** Modeled as a primary, top-level Candidate navigation destination (`CAN-11`).
- **Recruiter Application Detail:** Modeled as a dedicated full-screen `Application Detail & Review` dossier (`REC-06`) supporting applicant data, CV/resume inspection, attached Interview Results, and definitive binary adjudication (`Approve` / `Reject`).

---

## 2. Phase A — Final Pruned Screen Inventory

The master catalog below reconciles all platform screens and subviews.

| Screen ID | Screen Name | Role | UI Type | Purpose | Classification | Report Inclusion | Canonical Evidence | Notes |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| **PUB-01** | Public Landing Page | Guest | FULL SCREEN | Introduce platform value proposition, core pillars, and authentication entry points | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Overview, Route (`/`) | Primary public acquisition surface |
| **AUTH-01** | Account Registration | Guest | FULL SCREEN | Register account, select role (Candidate or Recruiter), and provide credentials | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Auth, Route (`/register`) | Role selection entry point |
| **AUTH-01-SUB1** | Email Verification Notice | Guest | SUBVIEW | Inform user of verification email dispatch with resend action | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Auth, Flow A | Kept for description; omitted from 3.1.1 graph |
| **AUTH-02** | Account Login | Registered User | FULL SCREEN | Authenticate credentials via Better Auth and resolve user session to role dashboard | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Auth, Route (`/login`) | Central authentication gateway |
| **AUTH-03** | Forgot Password Request | Registered User | FULL SCREEN | Submit registered email address to initiate password recovery workflow | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Auth, Route (`/forgot-password`) | Part of Forgot Password journey |
| **AUTH-03-SUB1** | Password Reset Link Dispatched Notice | Registered User | SUBVIEW | Acknowledge password recovery email dispatch | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Auth | Descriptive feedback state |
| **AUTH-04** | Set New Password | Registered User | FULL SCREEN | Set replacement password via emailed cryptographic recovery link | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Auth, Route (`/reset-password`) | Terminal screen of Forgot Password journey |
| **SHARED-01** | User Profile | Registered User | FULL SCREEN | View and update personal profile details, contact information, and role metadata | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Auth, Route (`/profile`) | Common account management hub |
| **SHARED-02** | Account Security & Password | Registered User | FULL SCREEN | Manage account security settings, credential updates, and multi-factor authentication | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Auth, Route (`/settings`) | Security settings hub |
| **SHARED-02-SUB1**| Change Password Modal | Registered User | SUBVIEW | Form to replace password using known current password | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Auth Use Case | Action modal inside SHARED-02 |
| **SHARED-02-SUB2**| Two-Factor Authentication Setup Modal | Registered User | SUBVIEW | Setup two-factor authentication via authenticator app QR code and verification token | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Auth Use Case | Configuration modal inside SHARED-02 |
| **CAN-01** | Candidate Dashboard | Candidate | FULL SCREEN | Primary workspace hub displaying recent sessions, practice recommendations, and status | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Core, Route (`/dashboard`) | Main candidate launchpad |
| **CAN-02** | Target JD Library | Candidate | FULL SCREEN | Manage personal library of saved, reviewed Target Job Descriptions | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain JD, Use Case | Ratified separate navigable screen |
| **CAN-03** | Add Target JD | Candidate | FULL SCREEN | Ingest target job description through raw text input or PDF document upload | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain JD, Route (`/interviews/new/job-description`) | Ingestion surface |
| **CAN-04** | Review & Refine Extracted JD | Candidate | FULL SCREEN | Review and edit AI-extracted competencies, adjust seniority, and add refinement notes | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain JD, Route (`/interviews/new/skills`) | Prerequisite to Blueprint compilation |
| **CAN-04-SUB1** | Refinement Notes Panel | Candidate | SUBVIEW | Dedicated input panel for candidate natural-language interview instructions | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain JD, Core Flows | Detailed subview within CAN-04 |
| **CAN-05** | Personal 3D Avatar Studio | Candidate | FULL SCREEN | Dedicated workspace for personal 3D avatar creation, VRM conversion, and library preview | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow C, Decision 7 | Ratified dedicated full screen |
| **CAN-05-SUB1** | Avaturn Embedded Experience | Candidate | SUBVIEW | Embedded free Avaturn iframe providing photo capture, preview, and GLB generation | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow C, Decision 7 | Primary interactive subview in studio |
| **CAN-05-SUB2** | Personal Avatar Confirmation & Preview | Candidate | SUBVIEW | View newly converted candidate-owned VRM avatar and save to practice profile | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Flow C | Post-conversion preview state |
| **CAN-06** | Configure Interview Session | Candidate | FULL SCREEN | Composite setup of 3D interviewer, voice profile, room, difficulty, and duration | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Interview, Decision 6 | Single composite setup step |
| **CAN-07** | Test Audio & Interview Readiness | Candidate | FULL SCREEN | Verify microphone input, audio playback readiness, and rendering capabilities | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Interview, Route (`/interviews/new/preflight`) | Preflight readiness check |
| **CAN-07-SUB1** | Readiness Device Error Dialog | Candidate | SUBVIEW | Troubleshooting guidance for microphone or hardware permission failure | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Interview | Error recovery modal; omitted from 3.1.1 |
| **CAN-08** | Live 3D Interview Room | Candidate | FULL SCREEN | Real-time spoken technical interview with 3D virtual avatar and adaptive question loop | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Interview, Route (`/interviews/[id]/room`) | Core simulation runtime |
| **CAN-08-SUB1** | Interview Session Pause / Exit Modal | Candidate | SUBVIEW | Modal dialog to pause active session, resume dialogue, or confirm early termination | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Interview, Core Flows | Navigational exit/resume branch |
| **CAN-08-SUB2** | 2D Waveform Fallback View | Candidate | SUBVIEW | Performance-resilient 2D audio waveform display when WebGL is unavailable | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Avatar-Voice, Decision 14 | Implementation fallback state |
| **CAN-09** | Interview Performance Report | Candidate | FULL SCREEN | Comprehensive diagnostic report displaying scores, feedback, and study roadmap | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Evaluation, Route (`/reports/[id]`) | Authoritative evaluation artifact |
| **CAN-09-SUB1** | Turn Critiques & Model Answers | Candidate | SUBVIEW | Dialogue breakdown comparing candidate responses against benchmark answers | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Evaluation | Detailed review panel within CAN-09 |
| **CAN-09-SUB2** | Actionable Learning Roadmap | Candidate | SUBVIEW | Prioritized technical study topics, documentation references, and practice tasks | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Evaluation | Study recommendations within CAN-09 |
| **CAN-09-SUB3** | Export Report Dialog | Candidate | SUBVIEW | Modal dialog to select export format and download the performance report | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Evaluation Use Case | Navigable export utility |
| **CAN-10** | Interview History | Candidate | FULL SCREEN | Chronological log of past practice sessions, scores, and links to reopen reports | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Interview, Route (`/history`) | Session record archive |
| **CAN-11** | Job Board (Browse Postings) | Candidate | FULL SCREEN | Search and filter approved recruiter job postings by technical stack and seniority | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow B, Decision 3 | Ratified primary destination |
| **CAN-12** | Job Posting Detail | Candidate | FULL SCREEN | View job requirements and locked company 3D interviewer model and voice profile | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow B | Job vacancy inspection surface |
| **CAN-13** | Job Application & CV Upload | Candidate | FULL SCREEN | Upload CV/resume and initiate required locked technical interview for the position | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow B, Decision 4 | Application entry point |
| **CAN-13-SUB1** | Application Submitted Confirmation | Candidate | SUBVIEW | Confirmation that interview result is attached to CV and submitted to recruiter | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Flow B | Completion state; moves to CAN-14 |
| **CAN-14** | My Applications Tracking | Candidate | FULL SCREEN | Track submitted applications and recruiter decisions (Pending, Approved, Rejected) | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow B, Use Case | Application tracking ledger |
| **CAN-14-SUB1** | Application Dossier & Result Viewer | Candidate | SUBVIEW | View submitted application details and associated Interview Result | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Flow B | Detail drawer inside CAN-14 |
| **CAN-15** | Membership & Billing | Candidate | FULL SCREEN | View active subscription status, renewal date, and available subscription options | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Payment, Route (`/billing`) | Membership subscription hub |
| **CAN-15-SUB1** | Payment Gateway Checkout Redirect | Candidate | SUBVIEW | Handoff interface to external electronic payment gateway portal | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Payment, Integrations | External checkout boundary |
| **CAN-15-SUB2** | Unsubscribe Confirmation Dialog | Candidate | SUBVIEW | Confirmation dialog to cancel active candidate recurring membership subscription | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Payment Use Case | Navigable cancellation action |
| **CAN-15-SUB3** | Payment Transaction History | Candidate | SUBVIEW | Ledger of past membership payment attempts, statuses, and receipts | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Payment Use Case | Subview tab inside CAN-15 |
| **REC-01** | Recruiter Dashboard | Recruiter | FULL SCREEN | Recruiter workspace hub displaying active postings, applicant counts, and actions | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Core, Use Case | Primary recruiter launchpad |
| **REC-02** | My Job Postings Management | Recruiter | FULL SCREEN | View, search, and filter company job postings across approval and active states | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Job-Posting, Use Case | Job posting management table |
| **REC-02-SUB1** | Archive Job Posting Dialog | Recruiter | SUBVIEW | Confirmation dialog to archive an inactive or filled company job posting | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Job-Posting Use Case | Action dialog inside REC-02 |
| **REC-03** | Create Job Posting | Recruiter | FULL SCREEN | Draft company job posting, extract skills, and configure company 3D interviewer | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow B, Decision 3 | Job posting authoring surface |
| **REC-03-SUB1** | AI Extraction Review & Confirmation | Recruiter | SUBVIEW | Review and confirm optional AI-extracted technical competency tags | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Flow B | Wizard step inside REC-03 |
| **REC-03-SUB2** | Configure Company 3D Interviewer & Voice | Recruiter | SUBVIEW | Select mandatory company 3D model and Voice Profile required for applicants | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Flow B | Setup step inside REC-03 |
| **REC-04** | Edit Job Posting | Recruiter | FULL SCREEN | Update requirements, description, or metadata of an existing company job posting | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Job-Posting Use Case | Modification surface |
| **REC-05** | Received Applications List | Recruiter | FULL SCREEN | Filter and review incoming candidate applications submitted to own job postings | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow B, Use Case | Application screening table |
| **REC-06** | Application Detail & Review | Recruiter | FULL SCREEN | Comprehensive dossier inspecting applicant contact, CV, and attached Interview Result | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow B, Decision 4 | Ratified dedicated full screen |
| **REC-06-SUB1** | Attached Interview Result Viewer | Recruiter | SUBVIEW | View the candidate's complete Performance Report and session turn transcript | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Flow B | Primary evaluation inspection panel |
| **REC-06-SUB2** | Application Adjudication Dialog | Recruiter | SUBVIEW | Binary decision modal to record definitive Approve or Reject status | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Flow B, Decision 4 | Scope termination action modal |
| **ADM-01** | Admin Governance Console | Administrator | FULL SCREEN | Platform operations hub summarizing moderation queue, active sessions, and revenue | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin, Route (`/admin`) | Primary governance dashboard |
| **ADM-02** | Account Governance (User List) | Administrator | FULL SCREEN | Search, filter, and inspect registered candidate and recruiter accounts | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin, Route (`/admin/users`) | Account security ledger |
| **ADM-03** | Account Detail & Lock/Unlock | Administrator | SUBVIEW | Inspect user profile history and execute administrative account lock or unlock | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | Security intervention drawer |
| **ADM-04** | Job Posting Moderation Queue | Administrator | FULL SCREEN | Review queue of submitted recruiter job postings awaiting administrative approval | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | Content moderation table |
| **ADM-05** | Job Posting Review & Approval | Administrator | FULL SCREEN | Inspect job description content, competencies, company interviewer, Approve/Reject | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | Moderation review surface |
| **ADM-05-SUB1** | Moderation Decision Dialog | Administrator | SUBVIEW | Confirmation dialog to record approval or rejection with reason note | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Admin Use Case | Decision dialog inside ADM-05 |
| **ADM-06** | Interview Sessions Oversight | Administrator | FULL SCREEN | Search, filter, and monitor platform interview sessions across operational statuses | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin, Route (`/admin/interviews`) | Operational session table |
| **ADM-07** | Interview Session Detail | Administrator | FULL SCREEN | Operational diagnostic view displaying session metadata, duration, and error flags | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | Diagnostic inspection view |
| **ADM-08** | Interview Feature Configuration | Administrator | FULL SCREEN | Configure platform-wide interview parameters, allowed duration limits, and toggles | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | Operational configuration view |
| **ADM-09** | AI Behaviour Management | Administrator | FULL SCREEN | Manage AI system prompt templates and Question guidance instructions | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | AI prompt governance surface |
| **ADM-09-SUB1** | Prompt Template Editor Drawer | Administrator | SUBVIEW | In-place editor drawer for conversational AI system prompt templates | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Admin Use Case | Editor drawer inside ADM-09 |
| **ADM-10** | Evaluation Criteria Calibration | Administrator | FULL SCREEN | Calibrate technical scoring rubrics, benchmark criteria, and competency weighting | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | Rubric calibration surface |
| **ADM-10-SUB1** | Rubric Template Editor Drawer | Administrator | SUBVIEW | In-place editor drawer for technical competency rubrics and scoring weights | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Admin Use Case | Editor drawer inside ADM-10 |
| **ADM-11** | Voice Profile Catalog | Administrator | FULL SCREEN | Curate provider-sourced TTS voice profiles available for mock interview simulations | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin, Route (`/admin/voices`) | Voice profile management table |
| **ADM-11-SUB1** | Delete Voice Profile Dialog | Administrator | SUBVIEW | Confirmation dialog to deactivate or delete obsolete voice profile | **DOC-ONLY** | 3.1.2, 3.1.3 | Brain Admin Use Case | Deletion modal inside ADM-11 |
| **ADM-12** | Fetch Voice Profiles Modal | Administrator | SUBVIEW | Query external TTS providers to discover and import new voice profiles | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | Provider fetch modal |
| **ADM-13** | Payment Transactions Ledger | Administrator | FULL SCREEN | Audit immutable financial ledger of all candidate membership checkout transactions | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin, Route (`/admin/billing`) | Financial governance ledger |
| **ADM-14** | Revenue Report Generator | Administrator | FULL SCREEN | Generate and view aggregated financial revenue reports by period (day, month, year) | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | Revenue reporting surface |
| **ADM-15** | Update Membership Price Modal | Administrator | SUBVIEW | Modal dialog to update the active monetary subscription price for memberships | **DRAW** | 3.1.1, 3.1.2, 3.1.3 | Brain Admin Use Case | Pricing governance modal |

---

## 3. Phase B — # 3.1.1 Navigation Flow Drawing Contract

This section defines the exact, clean, non-cluttered topology for the four Navigation Flows. Only nodes classified as **`DRAW`** are included.

---

### Flow 1: Public & Authentication Navigation Flow

#### Flow Metadata
- **Flow ID:** `FLOW-PUB-AUTH`
- **Flow Name:** Public & Authentication Navigation Flow
- **Primary Role:** Guest & Registered User
- **Entry Screen:** `PUB-01` (Public Landing Page)
- **Primary Exit Destinations:**
  - `CAN-01` (Candidate Dashboard upon Candidate login)
  - `REC-01` (Recruiter Dashboard upon Recruiter login)
  - `ADM-01` (Admin Governance Console upon Administrator login)

#### Nodes (5 Nodes)

| Node ID | Screen ID | Label | Group | Importance |
|:---|:---|:---|:---|:---|
| `N-PUB-01` | `PUB-01` | Public Landing Page | Public Acquisition | PRIMARY |
| `N-AUTH-01`| `AUTH-01` | Account Registration | Authentication | PRIMARY |
| `N-AUTH-02`| `AUTH-02` | Account Login | Authentication | PRIMARY |
| `N-AUTH-03`| `AUTH-03` | Forgot Password Request | Password Recovery | SECONDARY |
| `N-AUTH-04`| `AUTH-04` | Set New Password | Password Recovery | SECONDARY |

#### Edges (8 Edges)

| Edge ID | From | To | Navigation Meaning |
|:---|:---|:---|:---|
| `E-PA-01` | `N-PUB-01` | `N-AUTH-01` | Select Register / Get Started |
| `E-PA-02` | `N-PUB-01` | `N-AUTH-02` | Select Log In |
| `E-PA-03` | `N-AUTH-01`| `N-AUTH-02` | Submit registration / Return to Login |
| `E-PA-04` | `N-AUTH-02`| `N-AUTH-01` | Switch to Registration |
| `E-PA-05` | `N-AUTH-02`| `N-AUTH-03` | Select Forgot Password |
| `E-PA-06` | `N-AUTH-03`| `N-AUTH-02` | Cancel / Return to Login |
| `E-PA-07` | `N-AUTH-03`| `N-AUTH-04` | Open emailed recovery link |
| `E-PA-08` | `N-AUTH-04`| `N-AUTH-02` | Submit new password |

---

### Flow 2: Candidate Navigation Flow

#### Flow Metadata
- **Flow ID:** `FLOW-CANDIDATE`
- **Flow Name:** Candidate Navigation Flow
- **Primary Role:** Candidate
- **Entry Screen:** `CAN-01` (Candidate Dashboard)
- **Primary Exit Destinations:**
  - `CAN-09` (Interview Performance Report)
  - `CAN-14` (My Applications Tracking)
  - `CAN-01` (Candidate Dashboard)

#### Nodes (21 Nodes)

| Node ID | Screen ID | Label | Group | Importance |
|:---|:---|:---|:---|:---|
| `N-CAN-01` | `CAN-01` | Candidate Dashboard | Dashboard Hub | PRIMARY |
| `N-CAN-02` | `CAN-02` | Target JD Library | Target JD | SECONDARY |
| `N-CAN-03` | `CAN-03` | Add Target JD | Target JD | PRIMARY |
| `N-CAN-04` | `CAN-04` | Review & Refine Extracted JD | Target JD | PRIMARY |
| `N-CAN-05` | `CAN-05` | Personal 3D Avatar Studio | 3D Identity | SECONDARY |
| `N-CAN-05S1`| `CAN-05-SUB1`| Avaturn Embedded Experience | 3D Identity | PRIMARY |
| `N-CAN-06` | `CAN-06` | Configure Interview Session | Simulation Setup | PRIMARY |
| `N-CAN-07` | `CAN-07` | Test Audio & Interview Readiness | Simulation Execution | PRIMARY |
| `N-CAN-08` | `CAN-08` | Live 3D Interview Room | Simulation Execution | PRIMARY |
| `N-CAN-08S1`| `CAN-08-SUB1`| Interview Session Pause / Exit Modal | Simulation Execution | SUPPORTING |
| `N-CAN-09` | `CAN-09` | Interview Performance Report | Evaluation | PRIMARY |
| `N-CAN-09S3`| `CAN-09-SUB3`| Export Report Dialog | Evaluation | SUPPORTING |
| `N-CAN-10` | `CAN-10` | Interview History | History | SECONDARY |
| `N-CAN-11` | `CAN-11` | Job Board (Browse Postings) | Career Board | PRIMARY |
| `N-CAN-12` | `CAN-12` | Job Posting Detail | Career Board | PRIMARY |
| `N-CAN-13` | `CAN-13` | Job Application & CV Upload | Applications | PRIMARY |
| `N-CAN-14` | `CAN-14` | My Applications Tracking | Applications | SECONDARY |
| `N-CAN-15` | `CAN-15` | Membership & Billing | Membership | SECONDARY |
| `N-CAN-15S2`| `CAN-15-SUB2`| Unsubscribe Confirmation Dialog | Membership | SUPPORTING |
| `N-SH-01`  | `SHARED-01`| User Profile | Account | SECONDARY |
| `N-SH-02`  | `SHARED-02`| Account Security & Password | Account | SECONDARY |

#### Edges (27 Edges)

| Edge ID | From | To | Navigation Meaning |
|:---|:---|:---|:---|
| `E-CAN-01`| `N-CAN-01` | `N-CAN-03` | Start new practice session |
| `E-CAN-02`| `N-CAN-01` | `N-CAN-02` | View saved target JDs |
| `E-CAN-03`| `N-CAN-01` | `N-CAN-10` | View practice history |
| `E-CAN-04`| `N-CAN-01` | `N-CAN-11` | Explore job board |
| `E-CAN-05`| `N-CAN-01` | `N-CAN-14` | View tracked applications |
| `E-CAN-06`| `N-CAN-01` | `N-CAN-15` | Manage membership |
| `E-CAN-07`| `N-CAN-01` | `N-CAN-05` | Open personal avatar studio |
| `E-CAN-08`| `N-CAN-01` | `N-SH-01`  | Open user profile |
| `E-CAN-09`| `N-CAN-02` | `N-CAN-03` | Add new target JD |
| `E-CAN-10`| `N-CAN-02` | `N-CAN-04` | Reopen saved target JD |
| `E-CAN-11`| `N-CAN-03` | `N-CAN-04` | Submit raw JD for review |
| `E-CAN-12`| `N-CAN-04` | `N-CAN-06` | Approve JD requirements & proceed to setup |
| `E-CAN-13`| `N-CAN-05` | `N-CAN-05S1`| Launch Avaturn capture & customization |
| `E-CAN-14`| `N-CAN-05S1`| `N-CAN-05` | Save converted VRM avatar to studio library |
| `E-CAN-15`| `N-CAN-06` | `N-CAN-05` | Create personal avatar from interviewer picker |
| `E-CAN-16`| `N-CAN-06` | `N-CAN-07` | Confirm configuration & start readiness check |
| `E-CAN-17`| `N-CAN-07` | `N-CAN-08` | Readiness verified; enter live room |
| `E-CAN-18`| `N-CAN-08` | `N-CAN-08S1`| Pause live interview |
| `E-CAN-19`| `N-CAN-08S1`| `N-CAN-08` | Resume interview |
| `E-CAN-20`| `N-CAN-08S1`| `N-CAN-01` | Terminate session early; return home |
| `E-CAN-21`| `N-CAN-08` | `N-CAN-09` | Final question answered; view performance report |
| `E-CAN-22`| `N-CAN-09` | `N-CAN-09S3`| Open export report options |
| `E-CAN-23`| `N-CAN-09` | `N-CAN-06` | Repeat practice with this target JD |
| `E-CAN-24`| `N-CAN-10` | `N-CAN-09` | Select past session to inspect report |
| `E-CAN-25`| `N-CAN-11` | `N-CAN-12` | Inspect job posting details |
| `E-CAN-26`| `N-CAN-12` | `N-CAN-13` | Select Apply for position |
| `E-CAN-27`| `N-CAN-13` | `N-CAN-07` | Upload CV & launch required technical interview |
| `E-CAN-28`| `N-CAN-14` | `N-CAN-11` | Explore additional job postings |
| `E-CAN-29`| `N-CAN-15` | `N-CAN-15S2`| Open unsubscribe confirmation dialog |
| `E-CAN-30`| `N-SH-01`  | `N-SH-02`  | Navigate to account security |

---

### Flow 3: Recruiter Navigation Flow

#### Flow Metadata
- **Flow ID:** `FLOW-RECRUITER`
- **Flow Name:** Recruiter Navigation Flow
- **Primary Role:** Recruiter
- **Entry Screen:** `REC-01` (Recruiter Dashboard)
- **Primary Exit Destinations:**
  - `REC-02` (My Job Postings Management)
  - `REC-06` (Application Detail & Review)

#### Nodes (9 Nodes)

| Node ID | Screen ID | Label | Group | Importance |
|:---|:---|:---|:---|:---|
| `N-REC-01` | `REC-01` | Recruiter Dashboard | Dashboard Hub | PRIMARY |
| `N-REC-02` | `REC-02` | My Job Postings Management | Job Postings | PRIMARY |
| `N-REC-03` | `REC-03` | Create Job Posting | Job Postings | PRIMARY |
| `N-REC-04` | `REC-04` | Edit Job Posting | Job Postings | SECONDARY |
| `N-REC-05` | `REC-05` | Received Applications List | Applications | PRIMARY |
| `N-REC-06` | `REC-06` | Application Detail & Review | Applications | PRIMARY |
| `N-REC-06S1`| `REC-06-SUB1`| Attached Interview Result Viewer | Applications | PRIMARY |
| `N-SH-01`  | `SHARED-01`| User Profile | Account | SECONDARY |
| `N-SH-02`  | `SHARED-02`| Account Security & Password | Account | SECONDARY |

#### Edges (12 Edges)

| Edge ID | From | To | Navigation Meaning |
|:---|:---|:---|:---|
| `E-REC-01`| `N-REC-01` | `N-REC-03` | Select Create Job Posting |
| `E-REC-02`| `N-REC-01` | `N-REC-02` | Select Manage Postings |
| `E-REC-03`| `N-REC-01` | `N-REC-05` | Select Review Applications |
| `E-REC-04`| `N-REC-01` | `N-SH-01`  | Open recruiter profile |
| `E-REC-05`| `N-REC-02` | `N-REC-03` | Post new vacancy |
| `E-REC-06`| `N-REC-02` | `N-REC-04` | Edit existing posting |
| `E-REC-07`| `N-REC-02` | `N-REC-05` | Filter applications by posting |
| `E-REC-08`| `N-REC-03` | `N-REC-02` | Submit posting for admin approval |
| `E-REC-09`| `N-REC-04` | `N-REC-02` | Save posting updates |
| `E-REC-10`| `N-REC-05` | `N-REC-06` | Select candidate application to review |
| `E-REC-11`| `N-REC-06` | `N-REC-06S1`| Open attached Interview Result & transcript |
| `E-REC-12`| `N-SH-01`  | `N-SH-02`  | Navigate to account security |

---

### Flow 4: Administrator Navigation Flow

#### Flow Metadata
- **Flow ID:** `FLOW-ADMIN`
- **Flow Name:** Administrator Navigation Flow
- **Primary Role:** Administrator
- **Entry Screen:** `ADM-01` (Admin Governance Console)
- **Primary Exit Destinations:**
  - `ADM-02` (Account Governance)
  - `ADM-04` (Job Posting Moderation Queue)
  - `ADM-06` (Interview Sessions Oversight)
  - `ADM-11` (Voice Profile Catalog)
  - `ADM-13` (Payment Transactions Ledger)

#### Nodes (16 Nodes)

| Node ID | Screen ID | Label | Group | Importance |
|:---|:---|:---|:---|:---|
| `N-ADM-01` | `ADM-01` | Admin Governance Console | Governance Hub | PRIMARY |
| `N-ADM-02` | `ADM-02` | Account Governance (User List) | Account Security | PRIMARY |
| `N-ADM-03` | `ADM-03` | Account Detail & Lock/Unlock | Account Security | PRIMARY |
| `N-ADM-04` | `ADM-04` | Job Posting Moderation Queue | Content Moderation | PRIMARY |
| `N-ADM-05` | `ADM-05` | Job Posting Review & Approval | Content Moderation | PRIMARY |
| `N-ADM-06` | `ADM-06` | Interview Sessions Oversight | Session Monitoring | PRIMARY |
| `N-ADM-07` | `ADM-07` | Interview Session Detail | Session Monitoring | SECONDARY |
| `N-ADM-08` | `ADM-08` | Interview Feature Configuration | AI & Configuration | SECONDARY |
| `N-ADM-09` | `ADM-09` | AI Behaviour Management | AI & Configuration | PRIMARY |
| `N-ADM-10` | `ADM-10` | Evaluation Criteria Calibration | AI & Configuration | PRIMARY |
| `N-ADM-11` | `ADM-11` | Voice Profile Catalog | Voice Management | PRIMARY |
| `N-ADM-12` | `ADM-12` | Fetch Voice Profiles Modal | Voice Management | PRIMARY |
| `N-ADM-13` | `ADM-13` | Payment Transactions Ledger | Financial Governance | PRIMARY |
| `N-ADM-14` | `ADM-14` | Revenue Report Generator | Financial Governance | PRIMARY |
| `N-ADM-15` | `ADM-15` | Update Membership Price Modal | Financial Governance | PRIMARY |
| `N-SH-01`  | `SHARED-01`| User Profile | Account | SECONDARY |

#### Edges (19 Edges)

| Edge ID | From | To | Navigation Meaning |
|:---|:---|:---|:---|
| `E-ADM-01`| `N-ADM-01` | `N-ADM-02` | Open user accounts governance |
| `E-ADM-02`| `N-ADM-01` | `N-ADM-04` | Open job posting moderation queue |
| `E-ADM-03`| `N-ADM-01` | `N-ADM-06` | Open interview session monitoring |
| `E-ADM-04`| `N-ADM-01` | `N-ADM-08` | Open interview feature settings |
| `E-ADM-05`| `N-ADM-01` | `N-ADM-09` | Open AI prompt behavior governance |
| `E-ADM-06`| `N-ADM-01` | `N-ADM-10` | Open evaluation criteria calibration |
| `E-ADM-07`| `N-ADM-01` | `N-ADM-11` | Open TTS voice profile catalog |
| `E-ADM-08`| `N-ADM-01` | `N-ADM-13` | Open payment transactions audit ledger |
| `E-ADM-09`| `N-ADM-01` | `N-ADM-14` | Open revenue report generator |
| `E-ADM-10`| `N-ADM-01` | `N-SH-01`  | Open administrator profile |
| `E-ADM-11`| `N-ADM-02` | `N-ADM-03` | Inspect user account & open lock/unlock panel |
| `E-ADM-12`| `N-ADM-04` | `N-ADM-05` | Select pending posting for review |
| `E-ADM-13`| `N-ADM-05` | `N-ADM-04` | Submit moderation decision (Approve/Reject) |
| `E-ADM-14`| `N-ADM-06` | `N-ADM-07` | Inspect session diagnostic details |
| `E-ADM-15`| `N-ADM-07` | `N-ADM-06` | Return to session oversight list |
| `E-ADM-16`| `N-ADM-11` | `N-ADM-12` | Launch provider voice import modal |
| `E-ADM-17`| `N-ADM-13` | `N-ADM-14` | View aggregated revenue reports |
| `E-ADM-18`| `N-ADM-13` | `N-ADM-15` | Open pricing update modal |
| `E-ADM-19`| `N-ADM-14` | `N-ADM-15` | Open pricing update modal from revenue report |

---

## 4. Phase C — # 3.1.2 Screen Description Contract

This section defines every retained user-facing screen (`DRAW` and `DOC-ONLY`). Exactly 70 screens are documented sequentially. Each description explains what the user accomplishes on the screen in exactly one concise, technology-neutral sentence.

| # | Feature | Screen | Role | Description |
|:---:|:---|:---|:---|:---|
| 1 | Public & Acquisition | Public Landing Page | Guest | Explores platform capabilities, value proposition, and access points for registration and sign-in. |
| 2 | Authentication | Account Registration | Guest | Creates a new account by selecting an initial role and providing email credentials. |
| 3 | Authentication | Email Verification Notice | Guest | Views verification dispatch status and requests a replacement verification link if needed. |
| 4 | Authentication | Account Login | Registered User | Authenticates user credentials to access the appropriate role-specific workspace dashboard. |
| 5 | Authentication | Forgot Password Request | Registered User | Submits a registered email address to initiate the account password recovery procedure. |
| 6 | Authentication | Password Reset Link Dispatched Notice | Registered User | Reviews confirmation that a password reset recovery link has been dispatched to their email. |
| 7 | Authentication | Set New Password | Registered User | Submits a replacement password using the verified link received via email. |
| 8 | Account Management | User Profile | Registered User | Reviews and edits personal profile information, contact details, and role metadata. |
| 9 | Account Management | Account Security & Password | Registered User | Manages account security settings, credential updates, and two-factor authentication. |
| 10 | Account Management | Change Password Modal | Registered User | Replaces the active account password by supplying the current password and confirming a new one. |
| 11 | Account Management | Two-Factor Authentication Setup Modal | Registered User | Configures two-factor authentication by scanning an authenticator QR code and entering a token. |
| 12 | Candidate Workspace | Candidate Dashboard | Candidate | Reviews active practice recommendations, recent interview sessions, and application statuses. |
| 13 | Target Job Description | Target JD Library | Candidate | Manages personal collection of saved, reviewed, and custom target job descriptions. |
| 14 | Target Job Description | Add Target JD | Candidate | Submits a target job description by pasting raw text or uploading a PDF document. |
| 15 | Target Job Description | Review & Refine Extracted JD | Candidate | Inspects AI-extracted technical competencies, adjusts seniority, and adds refinement notes. |
| 16 | Target Job Description | Refinement Notes Panel | Candidate | Enters natural-language instructions to customize the technical focus of upcoming practice sessions. |
| 17 | Personal 3D Avatar | Personal 3D Avatar Studio | Candidate | Manages personal 3D avatars and launches the embedded creator experience. |
| 18 | Personal 3D Avatar | Avaturn Embedded Experience | Candidate | Captures reference photos and customizes personal 3D avatar features inside an embedded workspace. |
| 19 | Personal 3D Avatar | Personal Avatar Confirmation & Preview | Candidate | Previews newly converted personal 3D avatar and saves it to the personal practice library. |
| 20 | Interview Configuration | Configure Interview Session | Candidate | Configures interview execution parameters including 3D interviewer persona, voice, room, and difficulty. |
| 21 | Interview Simulation | Test Audio & Interview Readiness | Candidate | Tests microphone input, verifies audio output, and confirms device readiness before entering simulation. |
| 22 | Interview Simulation | Readiness Device Error Dialog | Candidate | Reviews troubleshooting guidance to resolve audio input or device permission issues. |
| 23 | Interview Simulation | Live 3D Interview Room | Candidate | Conducts an interactive spoken technical interview with an animated 3D virtual interviewer. |
| 24 | Interview Simulation | Interview Session Pause / Exit Modal | Candidate | Pauses an active interview session with options to resume dialogue or terminate early. |
| 25 | Interview Simulation | 2D Waveform Fallback View | Candidate | Continues spoken interview dialogue with an audio waveform interface when 3D rendering is unavailable. |
| 26 | Interview Evaluation | Interview Performance Report | Candidate | Reviews diagnostic interview performance scores, question critiques, and study recommendations. |
| 27 | Interview Evaluation | Turn Critiques & Model Answers | Candidate | Inspects question-by-question response critiques and recommended technical benchmarks. |
| 28 | Interview Evaluation | Actionable Learning Roadmap | Candidate | Reviews prioritized study recommendations, reference materials, and targeted practice topics. |
| 29 | Interview Evaluation | Export Report Dialog | Candidate | Configures and downloads an exportable copy of the completed interview performance report. |
| 30 | Practice History | Interview History | Candidate | Browses and filters past mock interview sessions to track performance progress over time. |
| 31 | Job Board | Job Board (Browse Postings) | Candidate | Searches and filters approved employer job openings by technology stack and seniority level. |
| 32 | Job Board | Job Posting Detail | Candidate | Inspects employer job requirements, company interview settings, and application instructions. |
| 33 | Job Application | Job Application & CV Upload | Candidate | Submits a CV/resume and launches the required company-defined interview simulation. |
| 34 | Job Application | Application Submitted Confirmation | Candidate | Receives confirmation that the completed interview result and CV were submitted to the recruiter. |
| 35 | Job Application | My Applications Tracking | Candidate | Monitors status progression of submitted job applications across employer review stages. |
| 36 | Job Application | Application Dossier & Result Viewer | Candidate | Re-examines submitted application materials and the associated technical interview result. |
| 37 | Membership & Payment | Membership & Billing | Candidate | Checks active membership subscription status, billing renewal dates, and available plans. |
| 38 | Membership & Payment | Payment Gateway Checkout Redirect | Candidate | Completes electronic payment checkout on an external payment provider portal. |
| 39 | Membership & Payment | Unsubscribe Confirmation Dialog | Candidate | Confirms the cancellation of recurring candidate membership subscription renewal. |
| 40 | Membership & Payment | Payment Transaction History | Candidate | Reviews an immutable audit ledger of past subscription payment attempts and receipts. |
| 41 | Recruiter Workspace | Recruiter Dashboard | Recruiter | Reviews summary metrics of active job postings, incoming applicants, and pending reviews. |
| 42 | Job Posting Management | My Job Postings Management | Recruiter | Manages company job postings across approval, active, rejected, and archived statuses. |
| 43 | Job Posting Management | Archive Job Posting Dialog | Recruiter | Confirms archiving an inactive or filled company job posting to remove it from public search. |
| 44 | Job Posting Management | Create Job Posting | Recruiter | Drafts a new employer opening, reviews extracted skills, and assigns company interviewer settings. |
| 45 | Job Posting Management | AI Extraction Review & Confirmation | Recruiter | Reviews and edits structured technical competency tags extracted from raw job description text. |
| 46 | Job Posting Management | Configure Company 3D Interviewer & Voice | Recruiter | Selects the company 3D interviewer model and voice profile that applicants must use. |
| 47 | Job Posting Management | Edit Job Posting | Recruiter | Updates requirements, employment details, or metadata for an existing company job opening. |
| 48 | Application Review | Received Applications List | Recruiter | Filters and manages candidate applications received for company job postings. |
| 49 | Application Review | Application Detail & Review | Recruiter | Inspects candidate profile, CV/resume, and attached Interview Result to make a hiring decision. |
| 50 | Application Review | Attached Interview Result Viewer | Recruiter | Examines the candidate's performance report, scores, and spoken dialogue transcript. |
| 51 | Application Review | Application Adjudication Dialog | Recruiter | Records a definitive binary Approve or Reject decision on an incoming candidate application. |
| 52 | Administration | Admin Governance Console | Administrator | Monitors platform operational metrics, pending moderation queues, and active interview sessions. |
| 53 | Account Governance | Account Governance (User List) | Administrator | Searches, filters, and inspects registered candidate and recruiter accounts. |
| 54 | Account Governance | Account Detail & Lock/Unlock | Administrator | Inspects detailed user account activity and applies or removes administrative security locks. |
| 55 | Content Moderation | Job Posting Moderation Queue | Administrator | Reviews recruiter job postings submitted for platform publication approval. |
| 56 | Content Moderation | Job Posting Review & Approval | Administrator | Inspects job description requirements and interviewer configurations to approve or reject a posting. |
| 57 | Content Moderation | Moderation Decision Dialog | Administrator | Records an administrative approval or rejection decision with mandatory feedback notes. |
| 58 | Session Oversight | Interview Sessions Oversight | Administrator | Searches, filters, and monitors technical mock interview sessions across the platform. |
| 59 | Session Oversight | Interview Session Detail | Administrator | Reviews diagnostic execution metadata, connection stability flags, and turn statistics for a session. |
| 60 | Configuration | Interview Feature Configuration | Administrator | Configures platform-wide interview parameters, runtime duration limits, and feature toggles. |
| 61 | AI Calibration | AI Behaviour Management | Administrator | Curates system prompt templates and Question guidance rules governing conversational interviewers. |
| 62 | AI Calibration | Prompt Template Editor Drawer | Administrator | Edits and updates conversational AI prompt templates used during live interview simulations. |
| 63 | Evaluation Calibration | Evaluation Criteria Calibration | Administrator | Calibrates evaluation scoring rubrics, benchmark criteria, and competency weighting. |
| 64 | Evaluation Calibration | Rubric Template Editor Drawer | Administrator | Modifies technical rubric definitions and scoring criteria for competency evaluation. |
| 65 | Voice Profile Management | Voice Profile Catalog | Administrator | Curates the catalog of provider-sourced TTS voice personas available for interview sessions. |
| 66 | Voice Profile Management | Delete Voice Profile Dialog | Administrator | Confirms the deactivation or removal of obsolete voice profiles from the system catalog. |
| 67 | Voice Profile Management | Fetch Voice Profiles Modal | Administrator | Queries external TTS providers to discover and import newly available synthesized voice models. |
| 68 | Financial Governance | Payment Transactions Ledger | Administrator | Audits an immutable financial transaction ledger of platform membership payments. |
| 69 | Financial Governance | Revenue Report Generator | Administrator | Generates aggregated financial reports analyzing platform revenue over selected time periods. |
| 70 | Financial Governance | Update Membership Price Modal | Administrator | Configures and saves the active monetary subscription price for candidate memberships. |

---

## 5. Phase D — # 3.1.3 Screen Authorization Contract

This matrix defines screen access permissions strictly by role ownership and canonical scope.

| Screen | Guest | Candidate | Recruiter | Administrator |
|:---|:---:|:---:|:---:|:---:|
| Public Landing Page | X | X | X | X |
| Account Registration | X |  |  |  |
| Email Verification Notice | X | X | X |  |
| Account Login | X |  |  |  |
| Forgot Password Request | X | X | X |  |
| Password Reset Link Dispatched Notice | X | X | X |  |
| Set New Password | X | X | X |  |
| User Profile |  | X | X | X |
| Account Security & Password |  | X | X | X |
| Change Password Modal |  | X | X | X |
| Two-Factor Authentication Setup Modal |  | X | X | X |
| Candidate Dashboard |  | X |  |  |
| Target JD Library |  | X |  |  |
| Add Target JD |  | X |  |  |
| Review & Refine Extracted JD |  | X |  |  |
| Refinement Notes Panel |  | X |  |  |
| Personal 3D Avatar Studio |  | X |  |  |
| Avaturn Embedded Experience |  | X |  |  |
| Personal Avatar Confirmation & Preview |  | X |  |  |
| Configure Interview Session |  | X |  |  |
| Test Audio & Interview Readiness |  | X |  |  |
| Readiness Device Error Dialog |  | X |  |  |
| Live 3D Interview Room |  | X |  |  |
| Interview Session Pause / Exit Modal |  | X |  |  |
| 2D Waveform Fallback View |  | X |  |  |
| Interview Performance Report |  | X |  |  |
| Turn Critiques & Model Answers |  | X |  |  |
| Actionable Learning Roadmap |  | X |  |  |
| Export Report Dialog |  | X |  |  |
| Interview History |  | X |  |  |
| Job Board (Browse Postings) |  | X |  |  |
| Job Posting Detail |  | X |  |  |
| Job Application & CV Upload |  | X |  |  |
| Application Submitted Confirmation |  | X |  |  |
| My Applications Tracking |  | X |  |  |
| Application Dossier & Result Viewer |  | X |  |  |
| Membership & Billing |  | X |  |  |
| Payment Gateway Checkout Redirect |  | X |  |  |
| Unsubscribe Confirmation Dialog |  | X |  |  |
| Payment Transaction History |  | X |  |  |
| Recruiter Dashboard |  |  | X |  |
| My Job Postings Management |  |  | X |  |
| Archive Job Posting Dialog |  |  | X |  |
| Create Job Posting |  |  | X |  |
| AI Extraction Review & Confirmation |  |  | X |  |
| Configure Company 3D Interviewer & Voice |  |  | X |  |
| Edit Job Posting |  |  | X |  |
| Received Applications List |  |  | X |  |
| Application Detail & Review |  |  | X |  |
| Attached Interview Result Viewer |  |  | X |  |
| Application Adjudication Dialog |  |  | X |  |
| Admin Governance Console |  |  |  | X |
| Account Governance (User List) |  |  |  | X |
| Account Detail & Lock/Unlock |  |  |  | X |
| Job Posting Moderation Queue |  |  |  | X |
| Job Posting Review & Approval |  |  |  | X |
| Moderation Decision Dialog |  |  |  | X |
| Interview Sessions Oversight |  |  |  | X |
| Interview Session Detail |  |  |  | X |
| Interview Feature Configuration |  |  |  | X |
| AI Behaviour Management |  |  |  | X |
| Prompt Template Editor Drawer |  |  |  | X |
| Evaluation Criteria Calibration |  |  |  | X |
| Rubric Template Editor Drawer |  |  |  | X |
| Voice Profile Catalog |  |  |  | X |
| Delete Voice Profile Dialog |  |  |  | X |
| Fetch Voice Profiles Modal |  |  |  | X |
| Payment Transactions Ledger |  |  |  | X |
| Revenue Report Generator |  |  |  | X |
| Update Membership Price Modal |  |  |  | X |

---

## 6. Phase E — # 3.1.4 Non-Screen Function Contract

This section defines the architectural background functions, processing pipelines, and internal orchestration mechanisms that execute without standalone user-facing screens.

| ID | Function | Trigger | Processing Summary | Result |
|:---:|:---|:---|:---|:---|
| **NS-01** | Credential Verification & Session Resolution | User submits login credentials | Better Auth validates submitted email and password hash, verifies account active status, and resolves role authorization context. | Authenticated session token issued and user routed to role dashboard. |
| **NS-02** | JD Parsing & Technical Competency Extraction | Raw JD text or PDF submitted | LLM semantic extraction parses text into normalized schema; deterministic rules validate categories and deduplicate skills. | Validated extracted competency payload delivered to review screen. |
| **NS-03** | Autonomous Interview Blueprint Compilation | Candidate approves JD and session configuration | System semantic planner synthesizes approved JD tags, refinement notes, and difficulty parameters into structured assessment stages and rubrics. | Immutable internal assessment blueprint compiled; strictly hidden from candidate. |
| **NS-04** | Runtime Question Orchestration | Candidate completes previous spoken response | LLM analyzes candidate's transcribed answer against interview context and blueprint milestones to determine the next conversational question. | Next runtime question text delivered to speech synthesis pipeline. |
| **NS-05** | Real-Time Speech-to-Text Processing | Candidate speaks into microphone | Streaming audio chunks are processed via Voice Activity Detection (VAD) and transcribed into clean text by the STT provider. | Accurate turn transcript emitted to interview orchestrator. |
| **NS-06** | Text-to-Speech & Viseme Synthesis | Question text generated | TTS provider synthesizes natural audio buffer while generating phoneme/viseme timing metadata frames. | Synchronized audio buffer and viseme timing frames delivered to client. |
| **NS-07** | 3D Lip-Sync & Viseme Interpolation | Synthesized audio playback begins | Client rendering engine interpolates humanoid avatar facial blend-shape morph targets in real-time synchronization with audio frames. | Realistic speech-synchronized mouth and facial animation rendered on WebGL canvas. |
| **NS-08** | Automated Turn & Competency Evaluation | Interview session concluded | LLM evaluation engine evaluates turn transcripts against session blueprint rubrics across 5 core competencies and computes weighted scores. | Immutable Performance Report created with scores, radar metrics, and recommendations. |
| **NS-09** | Avaturn GLB-to-VRM Asset Conversion | Avaturn iframe completes customization | Backend asset conversion pipeline ingests Avaturn GLB export, normalizes humanoid bone rigging into standardized VRM, and persists asset. | Persisted Candidate-owned VRM avatar stored in personal studio library. |
| **NS-10** | Job Application Assembly & Submission | Candidate finishes Job Posting interview | System links candidate CV/resume, application metadata, and resulting Interview Result into an application record with status PENDING. | Completed application dossier made available to the authoring recruiter. |
| **NS-11** | Payment Webhook Verification & Subscription Activation | Gateway dispatches payment status webhook | Gateway webhook signature is cryptographically verified; database transaction logs immutable ledger entry and updates subscription to ACTIVE. | Candidate practice entitlements activated idempotently. |
| **NS-12** | Disconnected Session Inactivity Cleanup | Session connection lost or abandoned | Automated system handler detects expired heartbeat or prolonged inactivity and gracefully terminates orphaned interview sessions. | Session resources released and incomplete session marked terminated. |
| **NS-13** | Transactional Notification Dispatch | Domain events occur (registration, password recovery, decision) | Asynchronous email dispatcher formats and transmits verification tokens, password recovery links, and application status notices. | Outbound transactional emails delivered to recipient mailboxes. |
| **NS-14** | Voice Profile Catalog Synchronization | Administrator triggers voice fetch | System queries external TTS provider APIs to discover available voice identifiers, languages, genders, and audio preview samples. | Newly available voice profiles ingested into platform governance catalog. |

---

## 7. Phase F — # FigJam Navigation Drawing Handoff

This block provides the exact, machine-actionable YAML specifications for drawing the four Navigation Flows in FigJam. Only `DRAW` nodes are included.

```yaml
# ==============================================================================
# FigJam Drawing Handoff: RoleCue Navigation Flows (DRAW Nodes Only)
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
    node_ids: [N-AUTH-01, N-AUTH-02, N-AUTH-03, N-AUTH-04]

nodes:
  - id: N-PUB-01
    screen_id: PUB-01
    label: Public Landing Page
    group: G-PUB
    importance: PRIMARY
  - id: N-AUTH-01
    screen_id: AUTH-01
    label: Account Registration
    group: G-AUTH
    importance: PRIMARY
  - id: N-AUTH-02
    screen_id: AUTH-02
    label: Account Login
    group: G-AUTH
    importance: PRIMARY
  - id: N-AUTH-03
    screen_id: AUTH-03
    label: Forgot Password Request
    group: G-AUTH
    importance: SECONDARY
  - id: N-AUTH-04
    screen_id: AUTH-04
    label: Set New Password
    group: G-AUTH
    importance: SECONDARY

edges:
  - id: E-PA-01
    from: N-PUB-01
    to: N-AUTH-01
    label: Register / Get Started
  - id: E-PA-02
    from: N-PUB-01
    to: N-AUTH-02
    label: Log In
  - id: E-PA-03
    from: N-AUTH-01
    to: N-AUTH-02
    label: Submit / Return to Login
  - id: E-PA-04
    from: N-AUTH-02
    to: N-AUTH-01
    label: Switch to Register
  - id: E-PA-05
    from: N-AUTH-02
    to: N-AUTH-03
    label: Forgot Password
  - id: E-PA-06
    from: N-AUTH-03
    to: N-AUTH-02
    label: Cancel
  - id: E-PA-07
    from: N-AUTH-03
    to: N-AUTH-04
    label: Open Recovery Link
  - id: E-PA-08
    from: N-AUTH-04
    to: N-AUTH-02
    label: Submit New Password

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
    node_ids: [N-CAN-02, N-CAN-03, N-CAN-04]
  - id: G-CAN-AVATAR
    label: Personal 3D Avatar Studio
    node_ids: [N-CAN-05, N-CAN-05S1]
  - id: G-CAN-SIM
    label: Interview Setup & Simulation
    node_ids: [N-CAN-06, N-CAN-07, N-CAN-08, N-CAN-08S1]
  - id: G-CAN-EVAL
    label: Evaluation & History
    node_ids: [N-CAN-09, N-CAN-09S3, N-CAN-10]
  - id: G-CAN-JOBS
    label: Job Board & Applications
    node_ids: [N-CAN-11, N-CAN-12, N-CAN-13, N-CAN-14]
  - id: G-CAN-BILLING
    label: Membership & Billing
    node_ids: [N-CAN-15, N-CAN-15S2]
  - id: G-CAN-ACCOUNT
    label: Account Management
    node_ids: [N-SH-01, N-SH-02]

nodes:
  - id: N-CAN-01
    screen_id: CAN-01
    label: Candidate Dashboard
    group: G-CAN-DASH
    importance: PRIMARY
  - id: N-CAN-02
    screen_id: CAN-02
    label: Target JD Library
    group: G-CAN-JD
    importance: SECONDARY
  - id: N-CAN-03
    screen_id: CAN-03
    label: Add Target JD
    group: G-CAN-JD
    importance: PRIMARY
  - id: N-CAN-04
    screen_id: CAN-04
    label: Review & Refine Extracted JD
    group: G-CAN-JD
    importance: PRIMARY
  - id: N-CAN-05
    screen_id: CAN-05
    label: Personal 3D Avatar Studio
    group: G-CAN-AVATAR
    importance: SECONDARY
  - id: N-CAN-05S1
    screen_id: CAN-05-SUB1
    label: Avaturn Embedded Experience
    group: G-CAN-AVATAR
    importance: PRIMARY
  - id: N-CAN-06
    screen_id: CAN-06
    label: Configure Interview Session
    group: G-CAN-SIM
    importance: PRIMARY
  - id: N-CAN-07
    screen_id: CAN-07
    label: Test Audio & Interview Readiness
    group: G-CAN-SIM
    importance: PRIMARY
  - id: N-CAN-08
    screen_id: CAN-08
    label: Live 3D Interview Room
    group: G-CAN-SIM
    importance: PRIMARY
  - id: N-CAN-08S1
    screen_id: CAN-08-SUB1
    label: Interview Session Pause / Exit Modal
    group: G-CAN-SIM
    importance: SUPPORTING
  - id: N-CAN-09
    screen_id: CAN-09
    label: Interview Performance Report
    group: G-CAN-EVAL
    importance: PRIMARY
  - id: N-CAN-09S3
    screen_id: CAN-09-SUB3
    label: Export Report Dialog
    group: G-CAN-EVAL
    importance: SUPPORTING
  - id: N-CAN-10
    screen_id: CAN-10
    label: Interview History
    group: G-CAN-EVAL
    importance: SECONDARY
  - id: N-CAN-11
    screen_id: CAN-11
    label: Job Board (Browse Postings)
    group: G-CAN-JOBS
    importance: PRIMARY
  - id: N-CAN-12
    screen_id: CAN-12
    label: Job Posting Detail
    group: G-CAN-JOBS
    importance: PRIMARY
  - id: N-CAN-13
    screen_id: CAN-13
    label: Job Application & CV Upload
    group: G-CAN-JOBS
    importance: PRIMARY
  - id: N-CAN-14
    screen_id: CAN-14
    label: My Applications Tracking
    group: G-CAN-JOBS
    importance: SECONDARY
  - id: N-CAN-15
    screen_id: CAN-15
    label: Membership & Billing
    group: G-CAN-BILLING
    importance: SECONDARY
  - id: N-CAN-15S2
    screen_id: CAN-15-SUB2
    label: Unsubscribe Confirmation Dialog
    group: G-CAN-BILLING
    importance: SUPPORTING
  - id: N-SH-01
    screen_id: SHARED-01
    label: User Profile
    group: G-CAN-ACCOUNT
    importance: SECONDARY
  - id: N-SH-02
    screen_id: SHARED-02
    label: Account Security & Password
    group: G-CAN-ACCOUNT
    importance: SECONDARY

edges:
  - id: E-CAN-01
    from: N-CAN-01
    to: N-CAN-03
    label: Start Practice
  - id: E-CAN-02
    from: N-CAN-01
    to: N-CAN-02
    label: View JD Library
  - id: E-CAN-03
    from: N-CAN-01
    to: N-CAN-10
    label: View History
  - id: E-CAN-04
    from: N-CAN-01
    to: N-CAN-11
    label: Explore Job Board
  - id: E-CAN-05
    from: N-CAN-01
    to: N-CAN-14
    label: Track Applications
  - id: E-CAN-06
    from: N-CAN-01
    to: N-CAN-15
    label: Membership & Billing
  - id: E-CAN-07
    from: N-CAN-01
    to: N-CAN-05
    label: Avatar Studio
  - id: E-CAN-08
    from: N-CAN-01
    to: N-SH-01
    label: User Profile
  - id: E-CAN-09
    from: N-CAN-02
    to: N-CAN-03
    label: Add New JD
  - id: E-CAN-10
    from: N-CAN-02
    to: N-CAN-04
    label: Reopen Saved JD
  - id: E-CAN-11
    from: N-CAN-03
    to: N-CAN-04
    label: Review Extracted JD
  - id: E-CAN-12
    from: N-CAN-04
    to: N-CAN-06
    label: Approve & Configure
  - id: E-CAN-13
    from: N-CAN-05
    to: N-CAN-05S1
    label: Open Avaturn Creator
  - id: E-CAN-14
    from: N-CAN-05S1
    to: N-CAN-05
    label: Save Converted VRM
  - id: E-CAN-15
    from: N-CAN-06
    to: N-CAN-05
    label: Create Avatar
  - id: E-CAN-16
    from: N-CAN-06
    to: N-CAN-07
    label: Test Readiness
  - id: E-CAN-17
    from: N-CAN-07
    to: N-CAN-08
    label: Enter Live Room
  - id: E-CAN-18
    from: N-CAN-08
    to: N-CAN-08S1
    label: Pause Session
  - id: E-CAN-19
    from: N-CAN-08S1
    to: N-CAN-08
    label: Resume
  - id: E-CAN-20
    from: N-CAN-08S1
    to: N-CAN-01
    label: Exit Early
  - id: E-CAN-21
    from: N-CAN-08
    to: N-CAN-09
    label: Complete Session
  - id: E-CAN-22
    from: N-CAN-09
    to: N-CAN-09S3
    label: Export Report
  - id: E-CAN-23
    from: N-CAN-09
    to: N-CAN-06
    label: Repeat Practice
  - id: E-CAN-24
    from: N-CAN-10
    to: N-CAN-09
    label: Reopen Report
  - id: E-CAN-25
    from: N-CAN-11
    to: N-CAN-12
    label: View Posting
  - id: E-CAN-26
    from: N-CAN-12
    to: N-CAN-13
    label: Apply for Job
  - id: E-CAN-27
    from: N-CAN-13
    to: N-CAN-07
    label: Start Job Interview
  - id: E-CAN-28
    from: N-CAN-14
    to: N-CAN-11
    label: Find More Jobs
  - id: E-CAN-29
    from: N-CAN-15
    to: N-CAN-15S2
    label: Cancel Subscription
  - id: E-CAN-30
    from: N-SH-01
    to: N-SH-02
    label: Security Settings

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
    node_ids: [N-REC-02, N-REC-03, N-REC-04]
  - id: G-REC-APPS
    label: Application Review & Decision
    node_ids: [N-REC-05, N-REC-06, N-REC-06S1]
  - id: G-REC-ACCOUNT
    label: Account Management
    node_ids: [N-SH-01, N-SH-02]

nodes:
  - id: N-REC-01
    screen_id: REC-01
    label: Recruiter Dashboard
    group: G-REC-DASH
    importance: PRIMARY
  - id: N-REC-02
    screen_id: REC-02
    label: My Job Postings Management
    group: G-REC-POSTINGS
    importance: PRIMARY
  - id: N-REC-03
    screen_id: REC-03
    label: Create Job Posting
    group: G-REC-POSTINGS
    importance: PRIMARY
  - id: N-REC-04
    screen_id: REC-04
    label: Edit Job Posting
    group: G-REC-POSTINGS
    importance: SECONDARY
  - id: N-REC-05
    screen_id: REC-05
    label: Received Applications List
    group: G-REC-APPS
    importance: PRIMARY
  - id: N-REC-06
    screen_id: REC-06
    label: Application Detail & Review
    group: G-REC-APPS
    importance: PRIMARY
  - id: N-REC-06S1
    screen_id: REC-06-SUB1
    label: Attached Interview Result Viewer
    group: G-REC-APPS
    importance: PRIMARY
  - id: N-SH-01
    screen_id: SHARED-01
    label: User Profile
    group: G-REC-ACCOUNT
    importance: SECONDARY
  - id: N-SH-02
    screen_id: SHARED-02
    label: Account Security & Password
    group: G-REC-ACCOUNT
    importance: SECONDARY

edges:
  - id: E-REC-01
    from: N-REC-01
    to: N-REC-03
    label: Create Job Posting
  - id: E-REC-02
    from: N-REC-01
    to: N-REC-02
    label: Manage Job Postings
  - id: E-REC-03
    from: N-REC-01
    to: N-REC-05
    label: Review Applications
  - id: E-REC-04
    from: N-REC-01
    to: N-SH-01
    label: User Profile
  - id: E-REC-05
    from: N-REC-02
    to: N-REC-03
    label: Post New Vacancy
  - id: E-REC-06
    from: N-REC-02
    to: N-REC-04
    label: Edit Posting
  - id: E-REC-07
    from: N-REC-02
    to: N-REC-05
    label: Filter Applications
  - id: E-REC-08
    from: N-REC-03
    to: N-REC-02
    label: Submit for Approval
  - id: E-REC-09
    from: N-REC-04
    to: N-REC-02
    label: Save Updates
  - id: E-REC-10
    from: N-REC-05
    to: N-REC-06
    label: Inspect Applicant Dossier
  - id: E-REC-11
    from: N-REC-06
    to: N-REC-06S1
    label: Open Attached Result
  - id: E-REC-12
    from: N-SH-01
    to: N-SH-02
    label: Security Settings

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
    label: Governance Hub
    node_ids: [N-ADM-01]
  - id: G-ADM-ACCOUNTS
    label: Account Security
    node_ids: [N-ADM-02, N-ADM-03]
  - id: G-ADM-MOD
    label: Content Moderation
    node_ids: [N-ADM-04, N-ADM-05]
  - id: G-ADM-SESSIONS
    label: Session Oversight
    node_ids: [N-ADM-06, N-ADM-07]
  - id: G-ADM-CALIB
    label: AI & Evaluation Calibration
    node_ids: [N-ADM-08, N-ADM-09, N-ADM-10]
  - id: G-ADM-VOICE
    label: Voice Profile Management
    node_ids: [N-ADM-11, N-ADM-12]
  - id: G-ADM-FINANCE
    label: Financial Governance
    node_ids: [N-ADM-13, N-ADM-14, N-ADM-15]
  - id: G-ADM-ACCOUNT
    label: Administrator Profile
    node_ids: [N-SH-01]

nodes:
  - id: N-ADM-01
    screen_id: ADM-01
    label: Admin Governance Console
    group: G-ADM-GOV
    importance: PRIMARY
  - id: N-ADM-02
    screen_id: ADM-02
    label: Account Governance (User List)
    group: G-ADM-ACCOUNTS
    importance: PRIMARY
  - id: N-ADM-03
    screen_id: ADM-03
    label: Account Detail & Lock/Unlock
    group: G-ADM-ACCOUNTS
    importance: PRIMARY
  - id: N-ADM-04
    screen_id: ADM-04
    label: Job Posting Moderation Queue
    group: G-ADM-MOD
    importance: PRIMARY
  - id: N-ADM-05
    screen_id: ADM-05
    label: Job Posting Review & Approval
    group: G-ADM-MOD
    importance: PRIMARY
  - id: N-ADM-06
    screen_id: ADM-06
    label: Interview Sessions Oversight
    group: G-ADM-SESSIONS
    importance: PRIMARY
  - id: N-ADM-07
    screen_id: ADM-07
    label: Interview Session Detail
    group: G-ADM-SESSIONS
    importance: SECONDARY
  - id: N-ADM-08
    screen_id: ADM-08
    label: Interview Feature Configuration
    group: G-ADM-CALIB
    importance: SECONDARY
  - id: N-ADM-09
    screen_id: ADM-09
    label: AI Behaviour Management
    group: G-ADM-CALIB
    importance: PRIMARY
  - id: N-ADM-10
    screen_id: ADM-10
    label: Evaluation Criteria Calibration
    group: G-ADM-CALIB
    importance: PRIMARY
  - id: N-ADM-11
    screen_id: ADM-11
    label: Voice Profile Catalog
    group: G-ADM-VOICE
    importance: PRIMARY
  - id: N-ADM-12
    screen_id: ADM-12
    label: Fetch Voice Profiles Modal
    group: G-ADM-VOICE
    importance: PRIMARY
  - id: N-ADM-13
    screen_id: ADM-13
    label: Payment Transactions Ledger
    group: G-ADM-FINANCE
    importance: PRIMARY
  - id: N-ADM-14
    screen_id: ADM-14
    label: Revenue Report Generator
    group: G-ADM-FINANCE
    importance: PRIMARY
  - id: N-ADM-15
    screen_id: ADM-15
    label: Update Membership Price Modal
    group: G-ADM-FINANCE
    importance: PRIMARY
  - id: N-SH-01
    screen_id: SHARED-01
    label: User Profile
    group: G-ADM-ACCOUNT
    importance: SECONDARY

edges:
  - id: E-ADM-01
    from: N-ADM-01
    to: N-ADM-02
    label: User Accounts
  - id: E-ADM-02
    from: N-ADM-01
    to: N-ADM-04
    label: Moderation Queue
  - id: E-ADM-03
    from: N-ADM-01
    to: N-ADM-06
    label: Session Oversight
  - id: E-ADM-04
    from: N-ADM-01
    to: N-ADM-08
    label: Feature Settings
  - id: E-ADM-05
    from: N-ADM-01
    to: N-ADM-09
    label: AI Behaviour
  - id: E-ADM-06
    from: N-ADM-01
    to: N-ADM-10
    label: Rubric Calibration
  - id: E-ADM-07
    from: N-ADM-01
    to: N-ADM-11
    label: Voice Profiles
  - id: E-ADM-08
    from: N-ADM-01
    to: N-ADM-13
    label: Payment Ledger
  - id: E-ADM-09
    from: N-ADM-01
    to: N-ADM-14
    label: Revenue Reports
  - id: E-ADM-10
    from: N-ADM-01
    to: N-SH-01
    label: Admin Profile
  - id: E-ADM-11
    from: N-ADM-02
    to: N-ADM-03
    label: Inspect & Lock/Unlock
  - id: E-ADM-12
    from: N-ADM-04
    to: N-ADM-05
    label: Review Posting
  - id: E-ADM-13
    from: N-ADM-05
    to: N-ADM-04
    label: Submit Decision
  - id: E-ADM-14
    from: N-ADM-06
    to: N-ADM-07
    label: Inspect Session
  - id: E-ADM-15
    from: N-ADM-07
    to: N-ADM-06
    label: Back to Sessions
  - id: E-ADM-16
    from: N-ADM-11
    to: N-ADM-12
    label: Fetch New Voices
  - id: E-ADM-17
    from: N-ADM-13
    to: N-ADM-14
    label: View Revenue Reports
  - id: E-ADM-18
    from: N-ADM-13
    to: N-ADM-15
    label: Update Price
  - id: E-ADM-19
    from: N-ADM-14
    to: N-ADM-15
    label: Adjust Base Price
```

---

## 8. Pruning Change Log

This log records every normalization, reclassification, pruning, and elimination action taken between the initial research pass and this final contract.

```text
1. Reclassified DRAW -> DOC-ONLY (22 Items)
   - AUTH-01-SUB1 (Email Verification Notice): Reclassified to DOC-ONLY; transient verification message.
   - AUTH-03-SUB1 (Password Reset Link Dispatched Notice): Reclassified to DOC-ONLY; transient feedback message.
   - SHARED-02-SUB1 (Change Password Modal): Reclassified to DOC-ONLY; action modal within Security screen.
   - SHARED-02-SUB2 (Two-Factor Auth Setup Modal): Reclassified to DOC-ONLY; action modal within Security screen.
   - CAN-04-SUB1 (Refinement Notes Panel): Reclassified to DOC-ONLY; input panel within Review & Refine screen.
   - CAN-05-SUB2 (Personal Avatar Confirmation & Preview): Reclassified to DOC-ONLY; post-conversion confirmation state.
   - CAN-07-SUB1 (Readiness Device Error Dialog): Reclassified to DOC-ONLY; transient hardware error modal.
   - CAN-08-SUB2 (2D Waveform Fallback View): Reclassified to DOC-ONLY; performance-resilient rendering fallback state.
   - CAN-09-SUB1 (Turn Critiques & Model Answers): Reclassified to DOC-ONLY; detailed feedback drawer/tab within Report.
   - CAN-09-SUB2 (Actionable Learning Roadmap): Reclassified to DOC-ONLY; study recommendation panel within Report.
   - CAN-13-SUB1 (Application Submitted Confirmation): Reclassified to DOC-ONLY; completion notice after job interview.
   - CAN-14-SUB1 (Application Dossier & Result Viewer): Reclassified to DOC-ONLY; inspection drawer within My Applications.
   - CAN-15-SUB1 (Payment Gateway Checkout Redirect): Reclassified to DOC-ONLY; external payment gateway boundary handoff.
   - CAN-15-SUB3 (Payment Transaction History): Reclassified to DOC-ONLY; ledger tab within Membership & Billing.
   - REC-02-SUB1 (Archive Job Posting Dialog): Reclassified to DOC-ONLY; action confirmation modal inside Postings list.
   - REC-03-SUB1 (AI Extraction Review & Confirmation): Reclassified to DOC-ONLY; wizard step inside Create Job Posting.
   - REC-03-SUB2 (Configure Company 3D Interviewer & Voice): Reclassified to DOC-ONLY; wizard step inside Create Job Posting.
   - REC-06-SUB2 (Application Adjudication Dialog): Reclassified to DOC-ONLY; final binary decision modal inside Application Detail.
   - ADM-05-SUB1 (Moderation Decision Dialog): Reclassified to DOC-ONLY; confirmation dialog inside Job Posting Review.
   - ADM-09-SUB1 (Prompt Template Editor Drawer): Reclassified to DOC-ONLY; editor drawer inside AI Behaviour Management.
   - ADM-10-SUB1 (Rubric Template Editor Drawer): Reclassified to DOC-ONLY; editor drawer inside Evaluation Calibration.
   - ADM-11-SUB1 (Delete Voice Profile Dialog): Reclassified to DOC-ONLY; confirmation modal inside Voice Profile Catalog.
   Rationale: Prevents graph clutter in 3.1.1 Navigation Flows while fully preserving interface definitions in 3.1.2 Screen Descriptions and 3.1.3 Authorization.

2. Reclassified Screen States -> NON-SCREEN Functions (2 Items)
   - CAN-03-SUB1 (AI Extraction In-Progress): Removed as a UI screen node; formalized under NS-02 (JD Parsing & Competency Extraction).
   - CAN-08-SUB3 (Evaluation Processing State): Removed as a UI screen node; formalized under NS-08 (Automated Turn Evaluation).
   Rationale: Transient loading spinners and backend AI evaluations do not represent navigable user destinations.

3. Removed Stale / Contradicted Items (6 Major Items)
   - Blueprint Preview & Confirmation (/interviews/new/blueprint): REMOVED. Contradicts Decision 1 (Blueprint strictly internal and hidden).
   - Admin 3D Avatar Catalog Management (/admin/avatars): REMOVED. Contradicts Decision 8 (Admin does not manage 3D meshes).
   - Practice Credit Balances & Wallets (/billing credits, no-credits): REMOVED. Contradicts Decision 12 (monetization via Membership only).
   - Practice Credit Packages Purchase (/billing/purchase-credits): REMOVED. Contradicts Decision 12 (no credit packages).
   - Public Marketing Pricing Page (/pricing for Guests): REMOVED. Contradicts Decision 11 (Guests limited to landing & registration).
   - Reset Password as Independent Formal Use Case: REMOVED as standalone use case; absorbed into Forgot Password journey (AUTH-04).
   Rationale: Strict enforcement of canonical Brain product decisions and elimination of prototype legacy artifacts.

4. Normalized Screen Names & Removed Speculative UI Detail
   - Normalized "5-Competency Radar & Learning Roadmap" to "Interview Performance Report" (CAN-09).
   - Normalized "Turn Critiques & Model Answers" to focus on diagnostic feedback and benchmark answers without assuming specific visual widgets.
   - Normalized "Job Application Dossier" to reflect candidate information, CV/resume, and attached Interview Result without inventing ATS stages.
   - Standardized all screen descriptions to exactly one concise, technology-neutral sentence focused on user accomplishments.

5. Ratified Human Decisions Applied
   - Decision 1 (Target JD Flow): Ratified 4 distinct navigable screen states (CAN-02, CAN-03, CAN-04, CAN-06).
   - Decision 2 (Personal 3D Avatar): Ratified dedicated full-screen studio (CAN-05) with embedded Avaturn iframe (CAN-05-SUB1).
   - Decision 3 (Job Board): Ratified primary top-level Candidate navigation destination (CAN-11).
   - Decision 4 (Recruiter Application Review): Ratified dedicated full-screen dossier (REC-06).
```

---

## 9. Remaining Human Decisions

**No material navigation decisions remain.**

All architectural boundaries, screen namespaces, role access models, and navigation topologies are fully resolved and aligned with the canonical RoleCue Brain.
