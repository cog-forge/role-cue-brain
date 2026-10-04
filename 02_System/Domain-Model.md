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
> Conceptual models and entity names in this document illustrate domain relationships and business invariants. They must **not** be interpreted as approved physical database migration scripts or finalized SQL schema specifications.

---

## 1. Conceptual Entity-Relationship Model

```mermaid
classDiagram
    class UserAccount {
        +UUID id
        +String email
        +String password_hash
        +Role role
        +AccountStatus status
    }

    class CandidateProfile {
        +UUID id
        +String full_name
        +String headline
        +String resume_url
    }

    class RecruiterProfile {
        +UUID id
        +String company_name
    }

    class CoinWallet {
        +UUID id
        +UUID user_id
        +Integer coin_balance
        +Timestamp updated_at
    }

    class PaymentOrder {
        +UUID id
        +UUID user_id
        +String gateway_order_ref
        +Decimal amount
        +String currency
        +PaymentStatus status
        +Timestamp created_at
    }

    class CoinTransaction {
        +UUID id
        +UUID wallet_id
        +CoinTxType type
        +Integer amount
        +String reference_id
        +Timestamp created_at
    }

    class TargetJD {
        +UUID id
        +String title
        +SeniorityLevel seniority_level
        +String raw_text
        +JSONB parsed_data
        +String refinement_notes
        +JDStatus status
    }

    class InterviewBlueprint {
        <<Internal Question Bank>>
        +UUID id
        +JSONB question_bank
        +Integer question_count
        +String contract_version
    }

    class PostingEvaluationSettings {
        <<Separate from Bank>>
        +UUID id
        +UUID job_posting_id
        +JSONB competency_weights
        +JSONB scoring_criteria
    }

    class JobPosting {
        +UUID id
        +UUID recruiter_id
        +String title
        +SeniorityLevel seniority_level
        +String description
        +Array~String~ required_technologies
        +String company_interviewer_model_id
        +UUID voice_profile_id
        +PostingStatus status
        +IntakeStatus intake_status
    }

    class InterviewSlot {
        +UUID id
        +UUID job_posting_id
        +SlotStatus status
        +Timestamp funded_at
        +Timestamp consumed_at
    }

    class Application {
        +UUID id
        +UUID job_posting_id
        +UUID candidate_id
        +JSONB candidate_info
        +String cv_resume_url
        +UUID interview_result_id
        +CVScreeningStatus cv_screening_status
        +ApplicationStatus final_status
        +Timestamp submitted_at
    }

    class InterviewSession {
        +UUID id
        +InterviewOrigin origin
        +InterviewStatus status
        +Integer current_turn_index
        +JSONB blueprint_snapshot
        +JSONB execution_context
        +String recording_url
        +Timestamp started_at
        +Timestamp ended_at
    }

    class SessionTurn {
        +UUID id
        +Integer turn_index
        +String question_text
        +String candidate_transcript
        +Float candidate_latency_sec
        +JSONB turn_evaluation
    }

    class PerformanceReport {
        +UUID id
        +Float overall_score
        +JSONB competency_scores
        +JSONB turn_critiques
        +JSONB learning_roadmap
    }

    class VoiceProfile {
        +UUID id
        +String provider_voice_id
        +String display_name
        +String language_code
        +String accent
        +String gender
        +Boolean is_active
    }

    class PersonalAvatar {
        +UUID id
        +UUID user_id
        +String model_vrm_url
        +String thumbnail_url
        +AvatarStatus status
    }

    %% Relationships
    UserAccount "1" -- "0..1" CandidateProfile : profile
    UserAccount "1" -- "0..1" RecruiterProfile : profile
    UserAccount "1" -- "0..1" CoinWallet : owns (Candidate or Recruiter)
    UserAccount "1" -- "0..*" PaymentOrder : places (Candidate or Recruiter)
    UserAccount "1" -- "0..*" PersonalAvatar : owns (Candidate or Recruiter)
    CoinWallet "1" -- "0..*" CoinTransaction : logs

    CandidateProfile "1" -- "0..*" TargetJD : owns
    CandidateProfile "1" -- "0..*" Application : submits

    TargetJD "1" -- "0..1" InterviewBlueprint : has current question bank
    JobPosting "1" -- "0..1" InterviewBlueprint : has current question bank
    JobPosting "1" -- "1" PostingEvaluationSettings : configures evaluation
    JobPosting "1" -- "0..*" InterviewSlot : holds funded capacity
    JobPosting "1" -- "0..*" Application : receives
    JobPosting "1" -- "1" VoiceProfile : requires
    JobPosting "1" -- "0..*" InterviewSession : configures when origin
    RecruiterProfile "1" -- "0..*" JobPosting : authors

    InterviewBlueprint "1" -- "0..*" InterviewSession : instantiated by (via snapshot)
    InterviewSession "1" -- "0..*" SessionTurn : records
    InterviewSession "1" -- "0..1" PerformanceReport : produces
    PerformanceReport "1" -- "0..1" Application : serves as Interview Result (attached after interview)
    VoiceProfile "1" -- "0..*" InterviewSession : voices
```

---

## 2. Entity Descriptions & Invariants

### 2.1. Identity, Wallets & Financials
* **`UserAccount`:** Root authentication record. Holds system role (`Candidate`, `Recruiter`, `Admin`) and status (`ACTIVE`, `LOCKED`).
* **`CandidateProfile`:** Profile metadata specific to candidates.
* **`RecruiterProfile`:** Employer identity metadata attached to a Recruiter account. Stores basic company identification without multi-tenant architecture.
* **`CoinWallet`:** Personal coin ledger owned individually by an authenticated **Candidate** or **Recruiter**. Tracks current balance and append-only internal transactions. Administrators do **NOT** have a wallet.
* **`PaymentOrder`:** Real-money checkout record processed via external Payment Gateways to acquire coin packages.
* **`CoinTransaction`:** Immutable ledger entry recording internal credits/debits (practice interview starts, interview slot funding, avatar generation fees, avatar capacity purchases, and End Recruitment unused slot refunds).

### 2.2. Practice & Simulation Pipeline
* **`TargetJD`:** The candidate's personal practice JD. Stores raw text, AI-extracted technical competencies, and natural-language `refinement_notes`.
* **`InterviewBlueprint`:** The internal assessment plan and **core-question bank**. Contains topic coverage matrices, a comprehensive bank of core questions, depth milestones, and grading rubrics.
  * **Invariant:** Exactly **at most one current Blueprint** exists per Target JD or Job Posting (absent until generated). Hidden from Candidates; Recruiter can view and edit core questions for their own Job Posting.
* **`PostingEvaluationSettings`:** Recruiter posting-level settings and weights across technical competencies, stored **separately** from the question bank Blueprint.
* **`InterviewSession`:** Concrete interview execution. Records whether the session originated from a Target JD (debited from Candidate wallet) or a Job Posting (consumes prepaid Recruiter interview slot). Preserves the source's execution context and `blueprint_snapshot`.
* **`SessionTurn`:** Granular dialogue unit within a session. Captures interviewer questions, candidate transcripts, audio timing, and real-time response analysis.
* **`PerformanceReport`:** Authoritative evaluation artifact produced from completed sessions. Holds overall score, competency breakdown, turn critiques, and learning roadmap.

### 2.3. Recruitment Board
* **`JobPosting`:** The company's Job Description authored by a Recruiter from JD-like content. Holds company 3D model and Voice Profile selections, links to its current question bank Blueprint and evaluation settings, and tracks intake status (`OPEN`, `CLOSED`).
* **`InterviewSlot`:** Unit of prepaid interview capacity allocated to a Job Posting, funded by the Recruiter using wallet coins. Consumed upon interview start; eligible unused slots are refunded as coins upon End Recruitment.
* **`Application`:** The Candidate's application to a `JobPosting`.
  * **Immediate Visibility:** Visible to the Recruiter immediately upon submission with uploaded CV/resume.
  * **Initial Result Optionality:** `interview_result_id` is initially null/optional; attached only after the approved applicant completes the required technical interview.
  * **Two Distinct Decisions:** Tracks initial `cv_screening_status` (`PENDING`, `CV_PASSED`, `CV_REJECTED`) and terminal `final_status` (`APPROVED`, `REJECTED`).

### 2.4. Audio-Visual Presentation & Avatar Inventory
* **`VoiceProfile`:** Voice configuration sourced from external TTS providers. Curated and managed by Administrators.
* **`PersonalAvatar`:** Humanoid 3D avatar stored as a standardized VRM. RoleCue converts Avaturn's final GLB to VRM, persists the asset, and associates it with the owning **Candidate or Recruiter** in their personal avatar inventory.
