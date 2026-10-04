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
| **`UserAccount`** | `CandidateProfile` | `1 : 0..1` | No | `CASCADE` | Unique candidate profile record per candidate account. |
| **`UserAccount`** | `RecruiterProfile` | `1 : 0..1` | No | `CASCADE` | Employer identity record per recruiter account. |
| **`UserAccount`** | `CoinWallet` | `1 : 0..1` | No | `CASCADE` | Personal coin wallet for Candidate or Recruiter; Admin has no wallet. |
| **`CoinWallet`** | `CoinTransaction` | `1 : 0..*` | No | `RESTRICT` | Append-only financial ledger entries must never be accidentally deleted. |
| **`UserAccount`** | `PaymentOrder` | `1 : 0..*` | No | `RESTRICT` | External real-money checkout records for coin packages. |
| **`UserAccount`** | `PersonalAvatar` | `1 : 0..*` | No | `CASCADE` | Candidate or Recruiter personal 3D avatars stored in their inventory. |
| **`UserAccount`** | `TargetJD` | `1 : 0..*` | No | `CASCADE` | Candidate owns zero or more practice target JDs. |
| **`TargetJD`** | `InterviewBlueprint` | `1 : 0..1` | No | `CASCADE` | Exactly at most one current core-question bank Blueprint per Target JD (absent until generated). |
| **`JobPosting`** | `InterviewBlueprint` | `1 : 0..1` | No | `RESTRICT` | Exactly at most one current core-question bank Blueprint per Job Posting (absent until generated). |
| **`JobPosting`** | `PostingEvaluationSettings` | `1 : 1` | No | `CASCADE` | Posting-level evaluation weights configured by Recruiter, stored separately from question bank. |
| **`JobPosting`** | `InterviewSlot` | `1 : 0..*` | No | `RESTRICT` | Prepaid interview capacity funded by Recruiter using coins from personal wallet. |
| **`JobPosting`** | `InterviewSession` | `1 : 0..*` | No | `RESTRICT` | Job Posting originates required technical interview sessions using locked presentation. |
| **`InterviewBlueprint`** | `InterviewSession` | `1 : 0..*` | No | `RESTRICT` | **CRITICAL:** Question bank is captured in session snapshot. Deleting blueprint must **NEVER** delete historical sessions. |
| **`InterviewSession`** | `SessionTurn` | `1 : 0..*` | No | `CASCADE` | Sequential conversational dialogue turns in a session. |
| **`InterviewSession`** | `PerformanceReport` | `1 : 0..1` | No (Unique) | `CASCADE` | Strictly at most one final report per session. |
| **`UserAccount`** | `JobPosting` | `1 : 0..*` | No | `RESTRICT` | Recruiter authors zero or more company Job Postings. |
| **`JobPosting`** | `Application` | `1 : 0..*` | No | `RESTRICT` | Applications received by an approved Job Posting. |
| **`UserAccount`** | `Application` | `1 : 0..*` | No | `RESTRICT` | Candidate submits zero or more applications. |
| **`Application`** | `PerformanceReport` | `0..1 : 0..1` | Yes | `RESTRICT` | Nullable initially; attached only after candidate passes CV screening and completes interview. |
| **`VoiceProfile`** | `InterviewSession` | `1 : 0..*` | Yes | `SET NULL` | Voice persona used by session; immutable execution context preserves historical presentation details. |
| **`VoiceProfile`** | `JobPosting` | `1 : 0..*` | No | `RESTRICT` | Approved Job Posting requires its Recruiter-selected company Voice Profile. |

---

## 2. Core Integrity Invariants

### 2.1. Historical Session Immutability
```mermaid
flowchart LR
    BP["Interview Blueprint (Question Bank)"] -->|"Instantiates via Snapshot"| SESS["Interview Session"]
    SESS -->|"Stores At Creation"| SNAP["blueprint_snapshot NOT NULL"]
    SNAP -->|"Immutable Reference"| REP["Performance Report"]
```
* **Invariant:** Every `InterviewSession` must persist an immutable `blueprint_snapshot` and execution context at the moment of session creation.
* **Integrity Guarantee:** Even if the source Target JD or Job Posting is updated, the question bank is edited, or evaluation weights change, completed historical evaluation reports and turn scores remain permanently reproducible and immune to retroactive mutation.

### 2.2. Blueprint Preservation via `ON DELETE RESTRICT`
* **Invariant:** The foreign key constraint between `InterviewSession` and `InterviewBlueprint` enforces `ON DELETE RESTRICT`.
* **Integrity Guarantee:** Deleting or replacing a Blueprint must be rejected if completed interview sessions reference it. Historical practice and recruitment records must never be orphaned.

### 2.3. Single Performance Report per Session
* **Invariant:** `PerformanceReport` enforces a strict `UNIQUE(session_id)` constraint.
* **Integrity Guarantee:** A completed interview session produces exactly one authoritative evaluation report. Re-submitting evaluation triggers cannot duplicate reports.

### 2.4. Independent Relational Data Scoping (No Multi-Tenancy)
* **Invariant:** RoleCue does not use multi-tenant schemas or Row-Level Security tenant isolation policies.
* **Integrity Guarantee:**
  * Recruiter data is partitioned relationally by `recruiter_id`.
  * Candidate data is partitioned relationally by `candidate_id` / `user_id`.
  * Recruiter and Candidate coin wallets are partitioned strictly by `user_id`.

### 2.5. Job Posting Application & CV-First Workflow Integrity
* **Invariant:** An `Application` is created and becomes visible to the owning Recruiter immediately upon candidate submission with an uploaded CV/resume.
* **Result Attachment:** The `interview_result_id` is initially nullable/optional (`0..1`). It is attached only after the candidate passes Recruiter CV screening and completes the required technical interview.
* **Integrity Guarantee:** Any Interview Result attached to an Application must originate from an `InterviewSession` whose Candidate and Job Posting match the Application's Candidate and Job Posting. The interview must execute the locked company presentation configured on the Job Posting.
* **Open Policy:** The maximum number of interview attempts permitted per application remains an open product decision; no hardcoded limit is assumed.

### 2.6. ACID Transactional Integrity for Coin Wallets & Payments
* **Invariant:** All updates to `PaymentOrder` status and `CoinTransaction` ledger movements must execute within explicit database transactions (`BEGIN ... COMMIT`).
* **Integrity Guarantee:** Prevents race conditions during webhook processing and ensures that internal coin deductions (practice interview start, interview slot funding, avatar generation fee, capacity purchases) and internal refunds (End Recruitment unused slot refund) maintain strict ledger balance consistency.
