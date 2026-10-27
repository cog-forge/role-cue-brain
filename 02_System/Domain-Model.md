---
title: Conceptual Domain Model
tags:
  - system
  - domain-model
  - entities
  - relationships
aliases:
  - Domain Model
  - Conceptual Model
---

# Conceptual Domain Model

This document maps the primary business entities of the RoleCue platform and illustrates their conceptual relationships, multiplicities, and boundaries.

> [!NOTE]
> The frozen RoleCue Use Case Diagram and frozen RoleCue ERD (named `RoleCue`) are authoritative.
> Conceptual models and entity names in this document illustrate domain relationships and business invariants conforming strictly to the frozen contract. RoleCue does not redesign payment models, propose V2/vNext schemas, or invent missing business rules.

---

## 1. Conceptual Entity-Relationship Model

```mermaid
classDiagram
    class User {
        +UUID id
        +String name
        +String email
        +Boolean email_verified
        +String image
        +UserRole role
        +Boolean is_locked
        +String lock_reason
        +String company_name
        +String company_website
        +Boolean two_factor_enabled
        +OnboardingStatus onboarding_status
    }

    class Account {
        +UUID id
        +UUID user_id
        +String account_id
        +String provider_id
        +String password
    }

    class Session {
        +UUID id
        +UUID user_id
        +String token
        +Timestamp expires_at
        +String ip_address
        +String user_agent
    }

    class Wallet {
        +UUID id
        +UUID user_id
        +Integer balance
        +Timestamp updated_at
    }

    class CoinPackage {
        +UUID id
        +String name
        +Decimal price
        +Integer amount
        +String currency
        +String description
    }

    class Transaction {
        +UUID id
        +UUID from
        +UUID to
        +Decimal amount
        +String currency
        +String description
        +TransactionStatus status
        +String payos_order_code
    }

    class Level {
        +UUID id
        +String name
    }

    class JobDescription {
        +UUID id
        +String raw_text
        +String refinement_note
        +String content_path
        +UUID level_id
    }

    class RoleProfile {
        +UUID id
        +UUID jd_id
        +UUID candidate_id
        +String status
        +Integer revision
    }

    class CoreQuestion {
        +UUID id
        +UUID jd_id
        +String content
    }

    class Inventory {
        +UUID id
        +UUID user_id
        +Integer slot
    }

    class Avatar {
        +UUID id
        +UUID inventory_id
        +String name
        +String model_path
        +String personality
    }

    class Voice {
        +UUID id
        +String name
        +Boolean is_active
    }

    class Metric {
        +UUID id
        +String name
        +String description
    }

    class MetricPercentage {
        +UUID id
        +UUID job_posting_id
        +UUID metric_id
        +Integer percentage
    }

    class JobPosting {
        +UUID id
        +UUID jd_id
        +Integer interview_slot
        +UUID required_avatar_id
        +UUID required_voice_id
        +UUID recruiter_id
        +String title
        +JobPostingStatus status
    }

    class Application {
        +UUID id
        +UUID job_posting_id
        +UUID candidate_id
        +String cv_path
        +Timestamp interview_deadline
        +ApplicationStatus status
        +String reject_reason
    }

    class Interview {
        +UUID id
        +UUID role_profile_id
        +InterviewType type
        +String record_path
        +UUID application_id
        +Float score
        +String feedback
        +UUID avatar_id
        +UUID voice_id
    }

    class InterviewQuestion {
        +UUID interview_id
        +Integer position
        +UUID core_question_id
    }

    class ConversationTurn {
        +UUID id
        +UUID interview_id
        +Integer position
        +String question
        +String answer
        +String feedback
    }

    class ScoreDetail {
        +UUID id
        +UUID interview_id
        +UUID metric_id
        +Float score
    }

    %% Relationships
    User "1" -- "0..*" Account : credentials
    User "1" -- "0..*" Session : sessions
    User "1" -- "0..1" Wallet : owns (Candidate or Recruiter)
    User "1" -- "0..1" Inventory : owns (Candidate or Recruiter)
    Inventory "1" -- "0..*" Avatar : contains
    Wallet "1" -- "0..*" Transaction : logs (from/to)
    CoinPackage "1" -- "0..*" Transaction : purchases

    Level "1" -- "0..*" JobDescription : categorizes
    JobDescription "1" -- "0..*" CoreQuestion : generates
    JobDescription "1" -- "0..1" JobPosting : defines requirements
    JobDescription "1" -- "0..*" RoleProfile : targets
    User "1" -- "0..*" RoleProfile : owns (Candidate)
    User "1" -- "0..*" JobPosting : authors (Recruiter)

    JobPosting "1" -- "0..*" MetricPercentage : weights
    Metric "1" -- "0..*" MetricPercentage : weighted in
    JobPosting "1" -- "1" Avatar : required avatar
    JobPosting "1" -- "1" Voice : required voice
    JobPosting "1" -- "0..*" Application : receives
    User "1" -- "0..*" Application : submits (Candidate)

    RoleProfile "1" -- "0..*" Interview : guides
    Application "1" <-- "0..1" Interview : fulfills recruitment interview (via application_id)
    Interview "1" -- "0..*" InterviewQuestion : instantiates
    CoreQuestion "1" -- "0..*" InterviewQuestion : referenced by
    Interview "1" -- "0..*" ConversationTurn : records
    Interview "1" -- "0..*" ScoreDetail : evaluates
    Metric "1" -- "0..*" ScoreDetail : metric score
    Avatar "1" -- "0..*" Interview : presents
    Voice "1" -- "0..*" Interview : speaks
```

---

## 2. Entity Descriptions & Invariants

### 2.1. Identity, Wallets & Financials
* **`User` (`users`):** Root authentication and identity record managed via Better Auth runtime. Stores basic profile (`name`, `email`, `email_verified`, `image`), role (`candidate`, `recruiter`, `admin`), lock state (`is_locked`, `lock_reason`), 2FA configuration (`two_factor_enabled`), onboarding status (`onboarding_status`), and company information (`company_name`, `company_website`) directly on the user record. There are no separate candidate or recruiter profile tables.
* **`Account` (`accounts`) & `Session` (`sessions`):** Better Auth credential tables. `accounts` stores third-party OAuth provider links and hashed local passwords. `sessions` stores active Better Auth session cookie records; a user may hold several concurrent sessions. RoleCue uses session cookies with `/api/auth/get-session` runtime validation; there is no custom JWT/refresh-token rotation layer.
* **`Wallet` (`wallets`):** Personal coin ledger owned individually by an authenticated **Candidate** or **Recruiter** (`user_id UNIQUE`). Holds `balance`. Administrators do **NOT** have a wallet.
* **`CoinPackage` (`coin_packages`):** Fixed coin purchasing tiers available for purchase via PayOS (`name`, `price`, `amount`, `currency`, `description`).
* **`Transaction` (`transactions`):** The unified financial ledger table. Records both real-money PayOS checkout orders (via `payos_order_code`) and internal coin balance transfers/refunds between wallets or system accounts using `from`, `to`, `amount`, `currency`, `description`, and `status` (`pending`, `success`, `failed`, `cancelled`).
  * *Accepted Technical Debt Note:* The team explicitly rejected the proposed payment architecture redesign (`payment_orders` + `coin_transactions`). The single unified `transactions` table is the authoritative model for this capstone.

### 2.2. Practice & Simulation Pipeline
* **`Level` (`levels`):** Seniority classification tier (`intern`, `junior`, `mid`, `senior`, `lead`).
* **`JobDescription` (`job_descriptions`):** Persistent job description record storing `raw_text`, natural-language `refinement_note`, `content_path` (for multi-page PDF documents), and `level_id REFERENCES levels(id)`. Serves as the requirements foundation for both candidate practice role profiles and recruiter job postings.
* **`RoleProfile` (`role_profiles`):** Candidate practice profile instance linking a candidate (`candidate_id REFERENCES users(id)`) to a target job description (`jd_id REFERENCES job_descriptions(id)`). Maintains workflow `status` and `revision`.
* **`CoreQuestion` (`core_questions`):** The persistent question bank. "Interview Blueprint" is a conceptual product term only; there is no Blueprint database table. Stores core questions linked to `job_descriptions.id` (`jd_id REFERENCES job_descriptions(id)`).
* **`Interview` (`interviews`):** Concrete interview execution (practice or recruitment). Captures `role_profile_id REFERENCES role_profiles(id)`, `type` (`practice` or `recruitment`), `record_path`, `score`, `feedback`, `avatar_id REFERENCES avatars(id)`, and `voice_id REFERENCES voices(id)`.
  * *Recruitment Application Link:* For recruitment interviews, `application_id uuid UNIQUE REFERENCES applications(id)` links the interview to the candidate's application. Enforced via database CHECK constraint: practice $\rightarrow$ `application_id IS NULL`; recruitment $\rightarrow$ `application_id IS NOT NULL`.
  * *Recruitment Slot Invariant:* For recruitment interviews, slot capacity is consumed upon the Candidate's **first successful interview start**. Reconnects or resumes do not consume additional capacity. Candidates are never charged.
* **`InterviewQuestion` (`interview_questions`):** Relational question assignment table mapping each session turn position (`(interview_id, position)`) to its source `core_question_id REFERENCES core_questions(id)`. Replaces unnormalized `blueprint_snapshot` column while ensuring historical interview reproducibility.
* **`ConversationTurn` (`conversation_turns`):** Granular dialogue unit within an interview session. Captures `interview_id`, `position`, `question`, `answer`, and turn-level `feedback`.
* **Evaluation & Scoring:** RoleCue persists evaluation outcomes directly in `interviews.score`, `interviews.feedback`, `score_details (interview_id, metric_id, score)`, and `conversation_turns.feedback`. There is no separate `performance_reports` table.

### 2.3. Recruitment Board
* **`JobPosting` (`job_postings`):** The company's Job Description authored by a Recruiter. References `jd_id UNIQUE REFERENCES job_descriptions(id)`, `recruiter_id REFERENCES users(id)`, `required_avatar_id REFERENCES avatars(id)`, `required_voice_id REFERENCES voices(id)`, `title`, and `status` (`pending`, `open`, `intake_closed`, `closed`, `rejected`).
  * **`interview_slot` Field:** Singular integer field representing maximum recruitment interview capacity (maximum number of Candidates that may be approved to proceed into the interview stage). It does **NOT** limit total CV submissions and does **NOT** represent company hiring headcount or final hires. RoleCue does NOT persist or enforce company hiring headcount.
  * **Evaluation Weights (`metrics` & `metrics_percentage`):** Separate from the core-question bank. Recruiters may save a subset of metrics with percentages totaling 100% via `metrics_percentage (job_posting_id, metric_id, percentage)`. Without custom rows, compute effective equal-split defaults on read (currently five active metrics at 20% each); do not persist default rows. `configured=false` and `using_defaults=true` are valid, and Admin approval must not require customization. Editing is allowed in `pending`/`rejected`, locked in `open`/`intake_closed`/`closed`, subject to the finalized technical state mapping (Decision 13a).
  * **Intake Lifecycle:**
    * **Ratified Publication Gate (2026-10-20):** Recruiter prepares and submits the Posting for moderation; Admin approves; Recruiter funds `interview_slot` with wallet coins; only after successful funding does Recruiter open Application Intake. Coin debit, unified `transactions` ledger entry, and `job_postings.interview_slot` update must be atomic and idempotent, with no partial effects or duplicate charges/capacity and no PayOS call.
    * **Intake Open:** Public discovery and applications require moderation approval, successful funding, and the Recruiter's subsequent Open Intake action. `status = 'open'` is the existing intake-open status, but current code setting it at approval alone is insufficient. Decision 13b's account-lock visibility restrictions also apply. Candidates may submit an unlimited number of Applications/CVs, immediately visible to the Recruiter in **View Application Detail**.
    * **Frozen Schema & Unresolved Mapping:** The enum contains only `pending`, `open`, `intake_closed`, `closed`, and `rejected`. There is no persisted `approved` Job Posting state or independent `intake_status` column. Moderation approval is conceptual; the persistence mapping for Approval → Funding → Open Intake is an unresolved implementation/design gap. No column, enum value, transition, or storage mechanism is added or assumed here; resolution requires evidence or product-owner approval (Decisions 13/16).
    * **Observed Implementation (Read-only review, 2026-10-20):** At RoleCue commit `dd83aa9b2332ff90db9d579cd4651f026eb53b4a`, the [recruitment migration](https://github.com/cog-forge/rolecue/blob/dd83aa9b2332ff90db9d579cd4651f026eb53b4a/api/migrations/000004_recruitment.up.sql) defines that enum and no separate intake column; [moderation service `Approve`](https://github.com/cog-forge/rolecue/blob/dd83aa9b2332ff90db9d579cd4651f026eb53b4a/api/internal/features/admin/moderation/service/service.go) passes `PostingStatusOpen`, and the [Job Posting domain model](https://github.com/cog-forge/rolecue/blob/dd83aa9b2332ff90db9d579cd4651f026eb53b4a/api/internal/features/jobposting/domain/model.go) says approval opens intake. This is incomplete implementation, not authorization to bypass funding or change the product contract.
    * **Close Intake:** Manual Recruiter action (`Close Job Posting Intake`) or automated System Handler action (`Close Job Posting When Meet Configured Limitation` when approved count reaches `interview_slot`). Transitions posting to `intake_closed` and automatically sets all unscreened/unapproved applications (`pending`) to terminal `rejected`. Already-approved candidates retain interview eligibility within their 24-hour deadline. There is **NO Reopen Intake** flow.
    * **Terminal Job Posting Close:** Automatically reached when EVERY application in scope has a terminal result (`approved` or `rejected`). Transitions posting to `closed`. Automatically triggers `Refund unused JP Candidate Slot` to refund still-unused capacity to Recruiter's wallet as internal coins. There is **NO manual End Recruitment** command.
* **`Application` (`applications`):** The Candidate's application to a `JobPosting`.
  * **Fields:** `job_posting_id`, `candidate_id`, `cv_path`, `interview_deadline`, `status` (`pending`, `interview_eligible`, `interviewed`, `approved`, `rejected`), `reject_reason`. Note that `applications` exists before the interview occurs and does **NOT** hold an `interview_id`; recruitment interviews link back to applications via `interviews.application_id`.
  * **Intake Submission:** Candidates submit CV/resume while intake is `open`; unlimited submissions permitted.
  * **CV Screening:** Recruiter reviews CV in **View Application Detail** and approves at most `interview_slot` Candidates (`interview_eligible`). CV approval grants eligibility without consuming a prepaid slot. Approval calculates and persists `interview_deadline = approval_time + 24 hours` (`approval_time` is conceptual/event-time only; only `applications.interview_deadline` is persisted).
  * **Interview Deadline Enforcement:** A Candidate must complete the recruitment interview before `interview_deadline`. Merely starting the interview before the deadline is insufficient. If a Candidate fails to complete their interview before `interview_deadline`, the System Handler automatically marks the application as terminal `rejected`.
  * **Hard-Gated Final Decisions:** Recruiter final Approve/Reject decisions are **HARD-GATED until MAX(interview_deadline)** across all interview-eligible applications for that posting. After the gate, the Recruiter renders final **Approve / Reject Application** decisions (`approved` or `rejected`) consolidated directly inside **View Application Detail**.

### 2.4. Audio-Visual Presentation & Avatar Inventory
* **`Inventory` (`inventories`):** Personal avatar inventory record owned by a **Candidate or Recruiter** (`user_id REFERENCES users(id)`), tracking allocated storage capacity `slot`. Ratified on **2026-10-20**: every Candidate and Recruiter starts with **3 storage slots**; Admin has no personal inventory. This is a product allocation requirement, not a claim about a database default. Slots are not generation credits; users can purchase additional capacity using wallet coins, and the frontend reads actual capacity from the authoritative API. Capacity purchases and generation fees are separate; the latter is charged only after RoleCue persists a usable VRM asset, never for failed/cancelled conversion or persistence. Exact generation and capacity prices remain unresolved (Decisions 7/16).
* **`Avatar` (`avatars`):** Humanoid 3D avatar stored as a standardized VRM asset (`inventory_id REFERENCES inventories(id)`, `name`, `model_path`, `personality`). RoleCue converts Avaturn's final GLB to VRM, persists the asset, and associates it with the owning user in their personal inventory.
* **`Voice` (`voices`):** Voice configuration sourced from external TTS providers (`name`, `is_active`). Curated and managed by Administrators.
