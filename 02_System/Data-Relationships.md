---
title: Data Relationships and Invariants
tags:
  - system
  - data-model
  - cardinalities
  - invariants
  - integrity
aliases:
  - Data Relationships
  - Entity Cardinalities
---

# Data Relationships & Cardinality Invariants

This document specifies the conceptual entity relationships, cardinalities, cascade behaviors, and lifecycle invariants governing data integrity across RoleCue.

---

## 1. Master Relationship Cardinality Matrix

Reconciled with the implemented schema (shared migrations `000001` through `000013`) on **2026-10-27**. The Cascade Policy column shows the implemented foreign-key action: `NO ACTION` (the PostgreSQL default) rejects deleting a referenced parent, with the same effect as `RESTRICT` for these tables. Rows marked *no FK* are logical relationships without a database foreign key.

| Parent Entity | Child Entity | Cardinality | Nullable FK? | Cascade Policy | Business Rationale |
| :--- | :--- | :---: | :---: | :--- | :--- |
| **`User` (`users`)** | `Account` (`accounts`) | `1 : 0..*` | No | `CASCADE` | Better Auth OAuth provider and hashed credential records per user. |
| **`User` (`users`)** | `Session` (`sessions`) | `1 : 0..*` | No | `CASCADE` | Better Auth active session cookie records; a user may hold several concurrent sessions under the single Better Auth session authority. |
| **`User` (`users`)** | `Verification` (`verifications`) | `1 : 0..*` | Yes | `NO ACTION` | Better Auth email verification and recovery tokens; `user_id` is optional. |
| **`User` (`users`)** | `TwoFactor` (`two_factors`) | `1 : 0..*` | No | `CASCADE` | TOTP two-factor secret, encrypted backup codes, and lockout state. |
| **`User` (`users`)** | `Wallet` (`wallets`) | `1 : 0..1` | No | `NO ACTION` | Personal coin wallet for Candidate or Recruiter (`UNIQUE(user_id)`); Admin has no wallet. |
| **`User` / `Wallet`** | `Transaction` (`transactions`) | `1 : 0..*` | — (*no FK*) | — | Unified ledger entries reference the user (or the literal `system` counterparty) as text in `from` / `to`; entries are append-only and never deleted. |
| **`CoinPackage` (`coin_packages`)** | `Transaction` (`transactions`) | `1 : 0..*` | — (*no FK*) | — | Real-money checkout purchases for coin packages via PayOS (`payos_order_code`, `currency = VND`); the package is identified in the order, not by a foreign key. |
| **`User` (`users`)** | `Inventory` (`inventories`) | `1 : 0..1` | No | `NO ACTION` | Candidate or Recruiter personal avatar storage (`UNIQUE(user_id)`, `CHECK slot >= 0`), initially 3 slots; Admin has no inventory (Decision 7). |
| **`Inventory` (`inventories`)** | `Avatar` (`avatars`) | `1 : 0..*` | No | `NO ACTION` | Standardized VRM personal 3D avatars stored in user inventory. |
| **`Level` (`levels`)** | `JobDescription` (`job_descriptions`) | `1 : 0..*` | Yes | `NO ACTION` | Seniority level tier classification (`intern`, `junior`, `mid`, `senior`, `lead`); optional until set. |
| **`JobDescription` (`job_descriptions`)** | `Requirement` (`requirements`) | `1 : 0..*` | No | `NO ACTION` | Extracted requirements classified by `kind`, optional `category`, and optional `type`; confirmed requirements feed question-bank generation. |
| **`JobDescription` (`job_descriptions`)** | `CoreQuestion` (`core_questions`) | `1 : 0..*` | No | `NO ACTION` | Core question bank (conceptual Blueprint) generated from confirmed requirements. |
| **`JobDescription` (`job_descriptions`)** | `RoleProfile` (`role_profiles`) | `1 : 0..1` | No (Unique) | `NO ACTION` | Candidate practice role profile referencing its own target job description (`UNIQUE(jd_id)`). |
| **`User` (`users`)** | `RoleProfile` (`role_profiles`) | `1 : 0..*` | No | `NO ACTION` | Candidate owns zero or more practice role profiles; Recruiters have none. |
| **`JobDescription` (`job_descriptions`)** | `JobPosting` (`job_postings`) | `1 : 0..1` | No (Unique) | `NO ACTION` | Job Posting requirements defined 1:1 by underlying job description (`UNIQUE(jd_id)`). |
| **`User` (`users`)** | `JobPosting` (`job_postings`) | `1 : 0..*` | No | `NO ACTION` | Recruiter authors zero or more company Job Postings. |
| **`JobPosting` (`job_postings`)** | `MetricPercentage` (`metrics_percentage`) | `1 : 0..*` | No | `NO ACTION` | Optional custom weights totaling 100%; absent rows use effective equal-split defaults, not persisted default rows (Decision 13a). |
| **`Metric` (`metrics`)** | `MetricPercentage` (`metrics_percentage`) | `1 : 0..*` | No | `NO ACTION` | Evaluation metric referenced in posting weights (primary key `(job_posting_id, metric_id)`). |
| **`JobPosting` (`job_postings`)** | `Application` (`applications`) | `1 : 0..*` | No | `NO ACTION` | Applications received by an open Job Posting. |
| **`User` (`users`)** | `Application` (`applications`) | `1 : 0..*` | No | `NO ACTION` | Candidate submits zero or more applications. |
| **`RoleProfile` (`role_profiles`)** | `Interview` (`interviews`) | `1 : 0..*` | Yes | `NO ACTION` | Practice interview guided by role profile requirements. `NULL` for recruitment; CHECK `type <> 'practice' OR role_profile_id IS NOT NULL`. |
| **`Application` (`applications`)** | `Interview` (`interviews`) | `1 : 0..1` | Yes (Unique) | `NO ACTION` | Recruitment interview references candidate application (`UNIQUE(application_id)`), and through it exactly one Job Posting; its owner is `applications.candidate_id`. Practice interviews have `application_id IS NULL`. |
| **`Interview` (`interviews`)** | `InterviewQuestion` (`interview_questions`) | `1 : 0..*` | No | `NO ACTION` | Session question assignment mapping turn position to source core question (primary key `(interview_id, position)`). |
| **`CoreQuestion` (`core_questions`)** | `InterviewQuestion` (`interview_questions`) | `1 : 0..*` | No | `NO ACTION` | Core question referenced in interview sessions. |
| **`Interview` (`interviews`)** | `ConversationTurn` (`conversation_turns`) | `1 : 0..*` | No | `NO ACTION` | Sequential conversational dialogue turns in an interview session. |
| **`Interview` (`interviews`)** | `ScoreDetail` (`score_details`) | `1 : 0..*` | No | `NO ACTION` | Evaluation score breakdown per metric (`UNIQUE(interview_id, metric_id)`). |
| **`Metric` (`metrics`)** | `ScoreDetail` (`score_details`) | `1 : 0..*` | No | `NO ACTION` | Platform evaluation metric evaluated in interview score details. |
| **`Avatar` (`avatars`)** | `Interview` (`interviews`) | `1 : 0..*` | Yes | `NO ACTION` | Avatar persona presented during interview session. |
| **`Voice` (`voices`)** | `Interview` (`interviews`) | `1 : 0..*` | Yes | `NO ACTION` | Voice persona speaking during interview session. |
| **`Avatar` (`avatars`)** | `JobPosting` (`job_postings`) | `1 : 0..*` | Yes | `NO ACTION` | Required company 3D avatar locked by Recruiter for Job Posting interview; nullable while the Posting is prepared, required before submission. |
| **`Voice` (`voices`)** | `JobPosting` (`job_postings`) | `1 : 0..*` | Yes | `NO ACTION` | Required company Voice Profile locked by Recruiter for Job Posting interview; nullable while the Posting is prepared, required before submission. |

---

## 2. Core Integrity Invariants

### 2.1. Historical Session Reproducibility via Relational Question Mapping
```mermaid
flowchart LR
    CQ["Core Question (core_questions)"] -->|"Referenced via FK"| IQ["Interview Question (interview_questions)"]
    INT["Interview (interviews)"] -->|"Assigns Position"| IQ
    INT -->|"Records Turns"| CT["Conversation Turns (conversation_turns)"]
    INT -->|"Records Scores"| SD["Score Details (score_details)"]
```
* **Invariant:** Every `Interview` maps its session turn positions to persistent `core_questions` via `interview_questions (interview_id, position, core_question_id)`.
* **Integrity Guarantee:** Even if the source Job Description or Job Posting is updated, foreign key constraints (which reject deleting a referenced parent) ensure historical interview questions, dialogue turns, and score evaluations remain permanently reproducible and tamper-proof without requiring an unnormalized `blueprint_snapshot` column.

### 2.2. Question Bank Preservation via `ON DELETE RESTRICT`
* **Invariant:** The foreign key constraint between `interview_questions` and `core_questions` rejects deleting a referenced core question (`NO ACTION`, equivalent to `RESTRICT` here).
* **Integrity Guarantee:** Deleting or replacing question banks must be rejected if completed interview sessions reference them. Historical practice and recruitment records must never be orphaned.

### 2.3. Consolidated Evaluation Storage & Score Detail Invariant
* **Invariant:** Evaluation scores are persisted directly on `interviews.score`, `interviews.feedback`, and `score_details (interview_id, metric_id, score)` with a strict `UNIQUE(interview_id, metric_id)` constraint. Turn-level critiques are stored in `conversation_turns.feedback`.
* **Integrity Guarantee:** A completed interview session produces an authoritative score breakdown across platform metrics. There is no separate `performance_reports` table; reporting is queried directly from relational interview and score detail tables.

### 2.4. Independent Relational Data Scoping (No Multi-Tenancy)
* **Invariant:** RoleCue does not use multi-tenant schemas or Row-Level Security tenant isolation policies.
* **Integrity Guarantee:**
  * Recruiter data is partitioned relationally by `recruiter_id`.
  * Candidate data is partitioned relationally by `candidate_id` / `user_id`.
  * Recruiter and Candidate coin wallets are partitioned strictly by `user_id`.

### 2.5. Job Posting Application & CV-First Workflow Integrity
* **Invariant (Intake Open):** Moderation approval → successful Recruiter wallet-coin funding → Recruiter Open Intake are required before Candidate discovery or applications. `open` alone does not establish these gates: the current interim implementation sets `open` at approval, so Candidates may apply before funding while CV approval and interview start still require funded capacity (known implementation gap); Decision 13b's Recruiter account-lock visibility restrictions also apply. The enum has only `pending`, `open`, `intake_closed`, `closed`, and `rejected`, with no persisted `approved` Job Posting status or independent `intake_status` column. Mapping the three steps onto that schema remains unresolved (Decisions 13/16; [[02_System/Domain-Model|Domain Model]]). Once intake is validly open, CV submissions are unlimited and immediately visible in `View Application Detail`; `interview_slot` does not limit submissions.
* **CV Screening & Deadline:** Recruiter approves at most `interview_slot` Candidates (`interview_eligible`). CV approval grants eligibility without consuming a prepaid slot. Approval calculates and persists `interview_deadline = approval_time + 24 hours` (`approval_time` is conceptual/event-time only; only `applications.interview_deadline` is persisted).
* **Deadline Enforcement:** A Candidate must complete the recruitment interview before `interview_deadline`. Merely starting the interview before the deadline is insufficient. After the deadline, answers and completion are refused; if an approved Candidate does not complete their interview before `interview_deadline`, the System Handler marks the application as terminal `rejected`.
* **Slot Consumption:** Slot capacity is consumed upon the Candidate's first successful interview start, when the Application's single recruitment session row is created (`UNIQUE(application_id)`); consumed capacity is the count of those sessions. Reconnects and resumes do not consume additional capacity. Candidates are never charged.
* **Hard-Gated Final Decisions:** Recruiter final decisions (`approved` or `rejected`) are **HARD-GATED until MAX(interview_deadline)** across all interview-eligible applications for that posting, consolidated in `View Application Detail`.
* **Close Intake:** `intake_closed` stops new submissions and rejects unscreened/unapproved applications while preserving eligible Candidates' deadline windows. Close Intake itself does not refund capacity; it evaluates terminal close in the same transaction, so a Posting with no application still in progress (including zero applications) closes at once. Repeating Close Intake on an `intake_closed` or `closed` Posting succeeds without change.
* **Terminal Job Posting Close & Slot Refund:** Terminal close is reached automatically from `intake_closed` when EVERY application in scope has a terminal result (`approved` or `rejected`), or there are none, transitioning posting status to `closed`. The System Handler executes `Refund unused JP Candidate Slot`, refunding `interview_slot` minus consumed recruitment sessions to the Recruiter's wallet as internal coins, exactly once on that transition, so nothing is double-credited or debited again. There is no manual End Recruitment flow and no Reopen Intake flow.

### 2.6. ACID Transactional Integrity for Unified Financial Transactions
* **Invariant:** All updates to the unified `transactions` ledger (`from`, `to`, `amount`, `currency`, `description`, `status`, `payos_order_code`) and associated wallet balance updates must execute within explicit database transactions (`BEGIN ... COMMIT`).
* **Funding Atomicity & Idempotency:** After moderation approval and before Recruiter opens intake, wallet coin debit, unified ledger entry, and `job_postings.interview_slot` update must commit as one atomic, idempotent operation. Funding happens exactly once per Posting at 1 coin per slot (no top-ups). Failures leave no partial debit or capacity; retries and duplicates cannot double-charge or allocate capacity twice. Funding does not invoke PayOS, which is exclusively for real-money coin-package top-ups.
* **Ledger Semantics & Revenue:** PayOS top-ups use `currency = VND`; internal coin movements use `COIN` with the literal `system` counterparty in `from`/`to`. Revenue is the sum of `VND` amounts of successful top-ups only. `transactions` has no timestamp column yet, so date-range revenue reporting remains open (Decision 16).
* **Integrity Guarantee:** Prevents race conditions during PayOS webhook verification and ensures that internal coin movements (practice interview starts, slot funding, avatar storage slot purchases, and terminal auto-close unused slot refunds) maintain strict ledger balance consistency.
* **Accepted Technical Debt:** The team formally rejected the split `payment_orders` / `coin_transactions` redesign; the single unified `transactions` table is authoritative for this capstone.

### 2.7. Personal Avatar Capacity & Charging
* **Ratified Initial Allocation (2026-10-20):** Every Candidate and Recruiter starts with **3 personal avatar storage slots**; Admin has no inventory. This is a business allocation rule, not an asserted SQL default.
* **Capacity Authority:** Users may purchase additional capacity with wallet coins. The frontend reads actual `inventories.slot` capacity through the authoritative API because it can increase.
* **No Generation Fee (Settled 2026-10-27):** Avatar creation only checks capacity (`INVENTORY_FULL` when no slot is free) and debits no coins. The only avatar charge is a capacity purchase (`POST /inventory/purchase`, 1–10 slots at 1 coin per slot), whose wallet debit, `transactions` entry, and `inventories.slot` increase commit atomically through the shared ledger (Decision 7).
