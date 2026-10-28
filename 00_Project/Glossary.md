---
title: Domain Glossary
tags:
  - glossary
  - terminology
  - domain-concepts
aliases:
  - Glossary
  - Terms
---

# Domain Glossary & Canonical Terminology

This document establishes unambiguous, locked definitions for core concepts across the RoleCue platform. All domain models, APIs, and product specifications must adhere to these definitions.

---

## 1. Primary Concept Distinctions

> [!IMPORTANT]
> ### Critical Distinction: Target JD vs. Job Posting
> * **`Job Posting` = The Company's Job Description.** It is created and managed by a **Recruiter**, then submitted for Admin moderation. After approval, Recruiter funds interview capacity, and successful funding opens intake; only then may Candidates discover and apply, subject to account-lock visibility rules (Decision 13b).
> * **`Target JD for Practice` = The Candidate's Practice Input.** It is pasted or uploaded by a **Candidate** strictly to drive their own personalized technical mock interview simulations. It is private to that candidate.
> 
> A Candidate may copy the text of a Job Posting to create a Target JD for Practice, but they represent **two distinct domain entities with different owners, lifecycles, and database storage**.

---

## 2. Core Domain Definitions

### Target JD for Practice
A Job Description provided by a Candidate (pasted plain text or uploaded PDF) to tailor a technical interview simulation. It contains target role requirements, expected technical stacks, and seniority expectations. Owned exclusively by the Candidate. Also referred to as Target JD or Role Profile practice input. A Role Profile (`role_profiles`) exists only for Candidate self-practice; Recruiters have no Role Profile.

### Job Posting
A formal job vacancy created by a Recruiter from JD-like content and representing an employer opening. It serves as the company's Job Description. A Recruiter confirms requirements, prepares the core-question bank, valid evaluation settings (optional custom weights or equal-split defaults), and company 3D interviewer model and Voice Profile before submitting for Admin moderation. Approval → successful wallet-coin funding (the Recruiter's Open Intake action) gates discovery and applications. The core-question bank is generated once and editable only while the Posting is `pending` or `rejected`; the Recruiter explicitly submits the Posting for review, which requires a non-empty bank, and Admin moderates only submitted Postings. The Job Posting enum has only `pending`, `open`, `intake_closed`, `closed`, and `rejected`, with no `approved` value or independent `intake_status` column. Persistence mapping (settled 2026-10-29): approval sets `open`; `open` with `interview_slot` unset is approved but not yet open (applications refused with `INTAKE_NOT_OPEN`); intake is open once `interview_slot` is set. Implementation alignment is in progress; until it lands, the interim implementation accepts applications before funding (Decisions 13/16). Recruitment workflow stops at Approve / Reject Application.

### Extracted JD
The structured, normalized technical competency data produced by the AI extraction pipeline from raw JD text. Consists of a validated title, optional seniority level, categorized technical skills (languages, frameworks, databases, tools, technologies, domain knowledge), and requirement flags (`required` vs. `preferred`). Soft/behavioral skills are excluded. Human confirmation of extracted requirements is required before Blueprint generation.

### JD Refinement Note
Natural-language customization instructions provided by the Candidate during the requirement review stage (e.g., *"Exclude C# from the interview"*, *"Emphasize microservices and event-driven architecture with Kafka"*). Confirmed alongside the approved requirements and used as input when generating the Interview Blueprint.

### Interview Configuration
The execution parameters that determine an interview's presentation and runtime context.
* **Target JD for Practice:** The Candidate chooses an available system 3D interviewer or eligible model from their **own** personal avatar inventory (never another user's), an active Voice Profile, a virtual 3D environment, difficulty (`easy`, `medium`, or `hard`), and duration.
* **Recruiter Job Posting:** The Recruiter selects the company 3D interviewer model and Voice Profile before Admin approval. Candidates must use those settings and cannot override them.

### Interview Blueprint
A conceptual and product term for the core-question bank generated following human confirmation of extracted requirements, skills, and seniority context. There is **no separate Blueprint table** in the database; persistent storage uses `core_questions`. Exactly **one current question bank** exists per JD (absent until generated). For a Job Posting it is generated once and editable only while the Posting is `pending` or `rejected`. It defines ONLY the persistent core-question bank. It does NOT contain grading rubrics, evaluation criteria, competency weights, depth benchmarks, or evaluation matrices.
* **Evaluation Configuration Separation:** Evaluation criteria, competency weights, and scoring settings are a separate concern from the question bank. Recruiters can configure posting-level evaluation weights on their Job Postings separately.
* **Access Boundary:** Strictly **internal and hidden from the Candidate**. Candidates never view, edit, or directly confirm an Interview Blueprint. Recruiters **can** view and edit core questions within the question bank generated for their own Job Postings.

### Interview Session
A concrete execution attempt of an interview (persisted as `interviews`). It may originate from a Candidate Target JD (practice, through the Candidate's Role Profile) or a Recruiter Job Posting (recruitment, through exactly one Application, with no Role Profile and presented under the Posting's title). Tracks real-time conversational turns (`conversation_turns`), candidate speech transcripts, Question delivery, audio playback, and (for recruitment) video/audio recordings (`record_path`). Explicitly links assigned questions via `interview_questions (interview_id, position, core_question_id)` and records evaluation results in `interviews.score`, `interviews.feedback`, and `score_details`.

### Question
The generic runtime unit spoken by the 3D interviewer during an Interview Session (modeled via `interview_questions`). An interview conducts a random selection of $x$ core questions from the question bank, with bounded follow-ups determined by candidate answers and interview context. Deciding whether to follow up and generating follow-up wording are decoupled responsibilities without premature vendor lock-in.

### Relational Session Historical Context (Replaces Blueprint Snapshot)
The immutable historical record established when an Interview Session is created. Rather than storing an unnormalized JSON `blueprint_snapshot` column, RoleCue relationally links each session turn position (`(interview_id, position)`) to its persistent `core_question_id REFERENCES core_questions(id)` in `interview_questions`. Custom posting weights are maintained in `metrics_percentage`; absent custom configuration, effective equal-split defaults are computed on read (Decision 13a). Results are recorded in `score_details` and `conversation_turns.feedback` and must not be recalculated when configurations change. Relational question references preserve historical question associations; they do not imply persisted default weights or a new evaluation snapshot mechanism.

### Application
A candidate submission to a Recruiter's Job Posting containing Candidate profile information and an uploaded CV/resume.
* **Unlimited Submissions:** While intake is open, Candidates may submit an unlimited number of Applications and CVs. Submissions are NOT capped by interview capacity.
* **Immediate Visibility:** The Application and CV are immediately visible to the owning Recruiter in View Application Detail upon submission.
* **Lifecycle & Decisions:** Progresses through a structured lifecycle: initial `PENDING` $\rightarrow$ Recruiter CV Screening Approval (grants interview eligibility with a 24-hour deadline: `interview_deadline = approval_time + 24 hours`; candidate must complete the interview before this deadline) or Rejection $\rightarrow$ technical interview execution (funded slot consumed on first start) $\rightarrow$ Hard-Gated Final Review (gated until `MAX(interview_deadline)`) $\rightarrow$ Recruiter Final Decision (`APPROVED` or `REJECTED`).
* **Terminal Rejection Paths:** Applications rejected during Close Intake cleanup, expired interview deadlines, CV screening rejections, and final Recruiter rejections all count as terminal rejected Applications.

### CV Screening Decision
The Recruiter's evaluation of a candidate's uploaded CV/resume inside View Application Detail. Recruiter may approve at most `interview_slot` Candidates to proceed to recruitment interview. Approving grants interview eligibility and sets a 24-hour deadline: `interview_deadline = approval_time + 24 hours` (the candidate must complete the interview before the deadline; `approval_time` is conceptual and only `interview_deadline` is persisted), but does not consume a funded slot yet.

### Final Decision
The definitive, terminal decision rendered by the Recruiter (`Approve` or `Reject` Application) inside View Application Detail after reviewing the candidate's complete profile, CV, technical Interview Result, and recruitment audio/video recordings and transcripts. Hard-gated until `MAX(interview_deadline)` across all interview-eligible Applications for the posting. Marks the strict termination of recruitment scope.

### Interview Result
The persisted evaluation outcome (Performance Report) of the required technical interview associated with a Job Posting. Attached to the Application alongside recruitment recordings for Recruiter review inside View Application Detail. Candidates can view their recruitment score, but cannot view recruitment transcripts or recordings during recruitment.

### Coin
The internal digital unit of value on RoleCue used to fund platform capabilities. Purchased in coin packages via PayOS using real money. Coins are used to pay for Candidate practice interview starts (5 coins), Recruiter interview slots (1 coin per slot), and additional avatar inventory capacity slots (1 coin per slot). There is no separate avatar generation fee.

### Wallet (Coin Wallet)
A personal digital coin balance ledger (`wallets`) held individually by a **Candidate** or **Recruiter**. Tracks current coin balance and ledger transactions. Administrators do **NOT** possess a wallet.

### Transaction (Unified Transaction Model)
An immutable financial ledger record (`transactions`) under RoleCue's unified transaction model, tracking both external real-money package orders (processed via PayOS with `payos_order_code`) and internal coin ledger movements (practice interview start fees, interview slot funding, avatar capacity purchases, and unused slot refunds upon terminal Job Posting close). Contains fields such as `from`, `to`, `amount`, `currency`, `description`, `status`, `payos_order_code`, and `created_at`; `currency` is `VND` for PayOS top-ups and `COIN` for internal movements, which use the `system` counterparty. Platform revenue counts only successful `VND` top-ups. PayOS is the confirmed real-money payment provider. The platform does NOT maintain separate `payment_orders` and `coin_transactions` tables.

### Interview Slot (`job_postings.interview_slot`)
The frozen ERD field defining maximum recruitment interview capacity (the maximum number of Candidates that may be approved to proceed into the recruitment interview stage). Funded exactly once per Posting at 1 coin per slot (no top-ups). One funded slot is consumed when an eligible applicant starts their first technical interview, that is, when the Application's single recruitment session is created. Resuming that same session does not consume another slot. Eligible unused slots (`interview_slot` minus consumed sessions) are refunded as coins once, upon terminal Job Posting auto-close. `interview_slot` does NOT represent total Applications/CVs submitted, company hiring headcount, or final hires. RoleCue does NOT persist or enforce company hiring headcount.

### Avatar Inventory Slot
A unit of storage capacity in a Candidate's or Recruiter's personal 3D avatar inventory. Every Candidate and Recruiter starts with **3 storage slots** (Decision 7, ratified 2026-10-20); Admin has no inventory. Each saved avatar occupies one slot; creating an avatar is free while a slot is available, with no separate generation fee (settled 2026-10-27). When full, creation is refused (`INVENTORY_FULL`) and users delete models (freeing their slots) or purchase additional capacity with wallet coins at 1 coin per slot (1–10 per purchase; purchased slots are permanent). The frontend reads actual capacity from the authoritative API.

### Close Intake
An operational action executed manually by a Recruiter (*Close Job Posting Intake*) or automatically by the System Handler (*Close Job Posting When Meet Configured Limitation* upon reaching `interview_slot` approved candidates). Halts receiving new Applications/CVs, automatically rejects all remaining unscreened or not-approved Applications, and preserves interview eligibility for already-approved Candidates within their 24-hour windows. Close Intake itself is NOT terminal Job Posting close and does NOT refund unused interview capacity; when no Application is still in progress (including a Posting with zero Applications), the same operation proceeds to terminal close, whose step refunds unused slots. Closing an already `intake_closed` or `closed` Posting again succeeds without change. There is NO Reopen Intake flow and NO manual End Recruitment command.

### Terminal Job Posting Close (Formerly End Recruitment)
The automated conclusion of a Job Posting, reached automatically once intake is closed and EVERY Application in scope has a terminal result (`APPROVED` or `REJECTED`), or immediately when there are no Applications. Automatically executes `Refund unused JP Candidate Slot` exactly once, refunding all still-unused recruitment interview capacity to the owning Recruiter's personal wallet as internal coins. There is NO manual End Recruitment command. Close Intake itself does NOT trigger refunds.

### Voice Profile
A speech synthesis persona sourced from Text-to-Speech (TTS) providers. Encapsulates provider identifiers, language, accent, gender, and vocal tone parameters. Sourced from TTS providers and managed exclusively by the **Admin**.

### Personal 3D Avatar
A customized 3D humanoid avatar created through RoleCue's embedded free Avaturn iframe experience. Avaturn handles photos, validation, preview, customization, and final GLB generation. RoleCue receives the GLB, converts it to VRM, persists the VRM production asset, and associates it with the owning Candidate or Recruiter in their avatar inventory. Creation costs no coins as long as the owner has a free storage slot; there is no separate generation fee.

### Membership Subscription (Superseded)
*Status: Superseded by Coin Wallets.* Formerly described recurring subscriptions and membership renewals. Completely replaced by coin packages, personal wallets, and slot funding.

### Core Technical Competencies
The evaluation dimensions used to score technical interview performance (`metrics`), separate from the Core Question Bank. For a Job Posting, Recruiter may select a metric subset with custom percentages totaling 100%; absent custom configuration, effective equal-split defaults apply (currently five active metrics at 20% each). `configured=false` and `using_defaults=true` are valid and do not block approval. Editing is allowed in `pending`/`rejected`, locked in `open`/`intake_closed`/`closed`, subject to the finalized technical state mapping (Decision 13a). Scores are persisted in `score_details`.

### Performance Report
The comprehensive evaluation summary generated upon completion of an Interview Session. Persisted directly across `interviews.score`, `interviews.feedback`, `score_details (interview_id, metric_id, score)`, and `conversation_turns.feedback`. Displays an overall score (0–100), competency breakdowns, radar chart, turn-by-turn question reviews with model answers, and a prioritized study roadmap.

### Blend-Shape Visemes
Facial morph target blend-shapes corresponding to phonemic sounds, used to animate the virtual interviewer's mouth in real-time synchronization with TTS audio.

### 2D Waveform Fallback Mode
A performance-resilient fallback interface displaying an animated audio waveform in place of the 3D canvas when client hardware lacks GPU acceleration or WebGL support, preserving uninterrupted voice interaction.
