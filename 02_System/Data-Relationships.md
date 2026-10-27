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

| Parent Entity | Child Entity | Cardinality | Nullable FK? | Cascade Policy | Business Rationale |
| :--- | :--- | :---: | :---: | :--- | :--- |
| **`User` (`users`)** | `Account` (`accounts`) | `1 : 0..*` | No | `CASCADE` | Better Auth OAuth provider and hashed credential records per user. |
| **`User` (`users`)** | `Session` (`sessions`) | `1 : 0..*` | No | `CASCADE` | Better Auth active session cookie records; a user may hold several concurrent sessions under the single Better Auth session authority. |
| **`User` (`users`)** | `Wallet` (`wallets`) | `1 : 0..1` | No | `CASCADE` | Personal coin wallet for Candidate or Recruiter (`UNIQUE(user_id)`); Admin has no wallet. |
| **`Wallet` (`wallets`)** | `Transaction` (`transactions`) | `1 : 0..*` | No | `RESTRICT` | Unified financial ledger entries (`from` / `to`) must never be accidentally deleted. |
| **`CoinPackage` (`coin_packages`)** | `Transaction` (`transactions`) | `1 : 0..*` | Yes | `RESTRICT` | Real-money checkout purchases for coin packages via PayOS (`payos_order_code`). |
| **`User` (`users`)** | `Inventory` (`inventories`) | `1 : 0..1` | No | `CASCADE` | Candidate or Recruiter personal avatar storage, initially 3 slots; Admin has no inventory (Decision 7). |
| **`Inventory` (`inventories`)** | `Avatar` (`avatars`) | `1 : 0..*` | No | `CASCADE` | Standardized VRM personal 3D avatars stored in user inventory. |
| **`Level` (`levels`)** | `JobDescription` (`job_descriptions`) | `1 : 0..*` | No | `RESTRICT` | Seniority level tier classification (`intern`, `junior`, `mid`, `senior`, `lead`). |
| **`JobDescription` (`job_descriptions`)** | `CoreQuestion` (`core_questions`) | `1 : 0..*` | No | `CASCADE` | Core question bank (conceptual Blueprint) generated from confirmed requirements. |
| **`JobDescription` (`job_descriptions`)** | `RoleProfile` (`role_profiles`) | `1 : 0..*` | No | `CASCADE` | Candidate practice role profiles referencing target job description. |
| **`User` (`users`)** | `RoleProfile` (`role_profiles`) | `1 : 0..*` | No | `CASCADE` | Candidate owns zero or more practice role profiles. |
| **`JobDescription` (`job_descriptions`)** | `JobPosting` (`job_postings`) | `1 : 0..1` | No (Unique) | `RESTRICT` | Job Posting requirements defined 1:1 by underlying job description (`UNIQUE(jd_id)`). |
| **`User` (`users`)** | `JobPosting` (`job_postings`) | `1 : 0..*` | No | `RESTRICT` | Recruiter authors zero or more company Job Postings. |
| **`JobPosting` (`job_postings`)** | `MetricPercentage` (`metrics_percentage`) | `1 : 0..*` | No | `CASCADE` | Optional custom weights totaling 100%; absent rows use effective equal-split defaults, not persisted default rows (Decision 13a). |
| **`Metric` (`metrics`)** | `MetricPercentage` (`metrics_percentage`) | `1 : 0..*` | No | `RESTRICT` | Evaluation metric referenced in posting weights (`UNIQUE(job_posting_id, metric_id)`). |
| **`JobPosting` (`job_postings`)** | `Application` (`applications`) | `1 : 0..*` | No | `RESTRICT` | Applications received by an open Job Posting. |
| **`User` (`users`)** | `Application` (`applications`) | `1 : 0..*` | No | `RESTRICT` | Candidate submits zero or more applications. |
| **`RoleProfile` (`role_profiles`)** | `Interview` (`interviews`) | `1 : 0..*` | No | `RESTRICT` | Interview simulation guided by role profile requirements. |
| **`Application` (`applications`)** | `Interview` (`interviews`) | `1 : 0..1` | Yes (Unique) | `RESTRICT` | Recruitment interview references candidate application (`UNIQUE(application_id)`). Practice interviews have `application_id IS NULL`. |
| **`Interview` (`interviews`)** | `InterviewQuestion` (`interview_questions`) | `1 : 0..*` | No | `CASCADE` | Session question assignment mapping turn position to source core question. |
| **`CoreQuestion` (`core_questions`)** | `InterviewQuestion` (`interview_questions`) | `1 : 0..*` | No | `RESTRICT` | Core question referenced in interview sessions. |
| **`Interview` (`interviews`)** | `ConversationTurn` (`conversation_turns`) | `1 : 0..*` | No | `CASCADE` | Sequential conversational dialogue turns in an interview session. |
| **`Interview` (`interviews`)** | `ScoreDetail` (`score_details`) | `1 : 0..*` | No | `CASCADE` | Evaluation score breakdown per metric (`UNIQUE(interview_id, metric_id)`). |
| **`Metric` (`metrics`)** | `ScoreDetail` (`score_details`) | `1 : 0..*` | No | `RESTRICT` | Platform evaluation metric evaluated in interview score details. |
| **`Avatar` (`avatars`)** | `Interview` (`interviews`) | `1 : 0..*` | Yes | `SET NULL` | Avatar persona presented during interview session. |
| **`Voice` (`voices`)** | `Interview` (`interviews`) | `1 : 0..*` | Yes | `SET NULL` | Voice persona speaking during interview session. |
| **`Avatar` (`avatars`)** | `JobPosting` (`job_postings`) | `1 : 0..*` | No | `RESTRICT` | Required company 3D avatar locked by Recruiter for Job Posting interview. |
| **`Voice` (`voices`)** | `JobPosting` (`job_postings`) | `1 : 0..*` | No | `RESTRICT` | Required company Voice Profile locked by Recruiter for Job Posting interview. |

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
* **Integrity Guarantee:** Even if the source Job Description or Job Posting is updated, foreign key constraints (`ON DELETE RESTRICT`) ensure historical interview questions, dialogue turns, and score evaluations remain permanently reproducible and tamper-proof without requiring an unnormalized `blueprint_snapshot` column.

### 2.2. Question Bank Preservation via `ON DELETE RESTRICT`
* **Invariant:** The foreign key constraint between `interview_questions` and `core_questions` enforces `ON DELETE RESTRICT`.
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
* **Integrity Guarantee:** Prevents race conditions during PayOS webhook verification and ensures that internal coin movements (practice interview starts, slot funding, avatar generation fees, and terminal auto-close unused slot refunds) maintain strict ledger balance consistency.
* **Accepted Technical Debt:** The team formally rejected the split `payment_orders` / `coin_transactions` redesign; the single unified `transactions` table is authoritative for this capstone.

### 2.7. Personal Avatar Capacity & Charging
* **Ratified Initial Allocation (2026-10-20):** Every Candidate and Recruiter starts with **3 personal avatar storage slots**; Admin has no inventory. This is a business allocation rule, not an asserted SQL default or three free generations.
* **Capacity Authority:** Users may purchase additional capacity with wallet coins. The frontend reads actual `inventories.slot` capacity through the authoritative API because it can increase.
* **Separate Generation Fee:** Charge only after RoleCue successfully persists a usable VRM asset. Failed/cancelled conversion or persistence has no generation fee. Capacity purchases (1 coin per slot) and generation fees are separate operations; the exact generation fee remains unresolved (Decisions 7/16).
