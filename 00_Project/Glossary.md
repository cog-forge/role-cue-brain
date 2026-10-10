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
> * **`Job Posting` = The Company's Job Description.** It is created, updated, and managed throughout its intake lifecycle by a **Recruiter** to advertise an open position at an employer, then submitted for Admin approval before public availability. Candidates browse approved Job Postings and submit Applications to them.
> * **`Target JD for Practice` = The Candidate's Practice Input.** It is pasted or uploaded by a **Candidate** strictly to drive their own personalized technical mock interview simulations. It is private to that candidate.
> 
> A Candidate may copy the text of a Job Posting to create a Target JD for Practice, but they represent **two distinct domain entities with different owners, lifecycles, and database storage**.

---

## 2. Core Domain Definitions

### Target JD for Practice
A Job Description provided by a Candidate (pasted plain text or uploaded PDF) to tailor a technical interview simulation. It contains target role requirements, expected technical stacks, and seniority expectations. Owned exclusively by the Candidate. Also referred to as Target JD or Role Profile practice input.

### Job Posting
A formal job vacancy created by a Recruiter from JD-like content and representing an employer opening. It serves as the company's Job Description. A Recruiter reviews and confirms AI-extracted structured requirements, edits core questions in the question bank, configures posting-level evaluation weights, and selects the company 3D interviewer model and Voice Profile before submitting for Admin approval. Only approved Job Postings are public. Recruitment workflow stops at Approve / Reject Application.

### Extracted JD
The structured, normalized technical competency data produced by the AI extraction pipeline from raw JD text. Consists of a validated title, optional seniority level, categorized technical skills (languages, frameworks, databases, tools, technologies, domain knowledge), and requirement flags (`required` vs. `preferred`). Soft/behavioral skills are excluded. Human confirmation of extracted requirements is required before Blueprint generation.

### JD Refinement Note
Natural-language customization instructions provided by the Candidate during the requirement review stage (e.g., *"Exclude C# from the interview"*, *"Emphasize microservices and event-driven architecture with Kafka"*). Confirmed alongside the approved requirements and used as input when generating the Interview Blueprint.

### Interview Configuration
The execution parameters that determine an interview's presentation and runtime context.
* **Target JD for Practice:** The Candidate chooses an available system 3D interviewer or eligible model from their personal avatar inventory, an available Voice Profile, a virtual 3D environment, difficulty (`easy`, `medium`, or `hard`), and duration.
* **Recruiter Job Posting:** The Recruiter selects the company 3D interviewer model and Voice Profile before Admin approval. Candidates must use those settings and cannot override them.

### Interview Blueprint
A conceptual and product term for the core-question bank generated following human confirmation of extracted requirements, skills, and seniority context. There is **no separate Blueprint table** in the database; persistent storage uses `core_questions`. Exactly **one current question bank** exists per JD (absent until generated). It defines ONLY the persistent core-question bank. It does NOT contain grading rubrics, evaluation criteria, competency weights, depth benchmarks, or evaluation matrices.
* **Evaluation Configuration Separation:** Evaluation criteria, competency weights, and scoring settings are a separate concern from the question bank. Recruiters can configure posting-level evaluation weights on their Job Postings separately.
* **Access Boundary:** Strictly **internal and hidden from the Candidate**. Candidates never view, edit, or directly confirm an Interview Blueprint. Recruiters **can** view and edit core questions within the question bank generated for their own Job Postings.

### Interview Session
A concrete execution attempt of an interview (persisted as `interviews`). It may originate from a Candidate Target JD (practice) or a Recruiter Job Posting (recruitment). Tracks real-time conversational turns (`conversation_turns`), candidate speech transcripts, Question delivery, audio playback, and (for recruitment) video/audio recordings (`record_path`). Explicitly links assigned questions via `interview_questions (interview_id, position, core_question_id)` and records evaluation results in `interviews.score`, `interviews.feedback`, and `score_details`.

### Question
The generic runtime unit spoken by the 3D interviewer during an Interview Session (modeled via `interview_questions`). An interview conducts a random selection of $x$ core questions from the question bank, with bounded follow-ups determined by candidate answers and interview context. Deciding whether to follow up and generating follow-up wording are decoupled responsibilities without premature vendor lock-in.

### Relational Session Historical Context (Replaces Blueprint Snapshot)
The immutable historical record established when an Interview Session is created. Rather than storing an unnormalized JSON `blueprint_snapshot` column, RoleCue relationally links each session turn position (`(interview_id, position)`) to its persistent `core_question_id REFERENCES core_questions(id)` in `interview_questions`. Evaluation weights are maintained in `metrics_percentage`, while results are recorded in `score_details` and `conversation_turns.feedback`. This relational architecture ensures that historical evaluations and questions remain 100% reproducible and tamper-proof even if the parent JD or question bank evolves.

### Application
A candidate submission to a Recruiter's Job Posting containing Candidate profile information and an uploaded CV/resume.
* **Unlimited Submissions:** While intake is open, Candidates may submit an unlimited number of Applications and CVs. Submissions are NOT capped by interview capacity.
* **Immediate Visibility:** The Application and CV are immediately visible to the owning Recruiter in View Application Detail upon submission.
* **Lifecycle & Decisions:** Progresses through a structured lifecycle: initial `PENDING` $\rightarrow$ Recruiter CV Screening Approval (grants interview eligibility with a 24-hour deadline: `interview_deadline = cv_approved_at + 24 hours`) or Rejection $\rightarrow$ technical interview execution (funded slot consumed on first start) $\rightarrow$ Hard-Gated Final Review (gated until `MAX(interview_deadline)`) $\rightarrow$ Recruiter Final Decision (`APPROVED` or `REJECTED`).
* **Terminal Rejection Paths:** Applications rejected during Close Intake cleanup, expired interview deadlines, CV screening rejections, and final Recruiter rejections all count as terminal rejected Applications.

### CV Screening Decision
The Recruiter's evaluation of a candidate's uploaded CV/resume inside View Application Detail. Recruiter may approve at most `interview_slot` Candidates to proceed to recruitment interview. Approving grants interview eligibility and sets a 24-hour deadline (`cv_approved_at + 24 hours`), but does not consume a funded slot yet.

### Final Decision
The definitive, terminal decision rendered by the Recruiter (`Approve` or `Reject` Application) inside View Application Detail after reviewing the candidate's complete profile, CV, technical Interview Result, and recruitment audio/video recordings and transcripts. Hard-gated until `MAX(interview_deadline)` across all interview-eligible Applications for the posting. Marks the strict termination of recruitment scope.

### Interview Result
The persisted evaluation outcome (Performance Report) of the required technical interview associated with a Job Posting. Attached to the Application alongside recruitment recordings for Recruiter review inside View Application Detail. Candidates can view their recruitment score, but cannot view recruitment transcripts or recordings during recruitment.

### Coin
The internal digital unit of value on RoleCue used to fund platform capabilities. Purchased in coin packages via PayOS using real money. Coins are used to pay for Candidate practice interview starts, Recruiter interview slots, avatar generation fees, and additional avatar inventory capacity slots.

### Wallet (Coin Wallet)
A personal digital coin balance ledger (`wallets`) held individually by a **Candidate** or **Recruiter**. Tracks current coin balance and ledger transactions. Administrators do **NOT** possess a wallet.

### Transaction (Unified Transaction Model)
An immutable financial ledger record (`transactions`) under RoleCue's unified transaction model, tracking both external real-money package orders (processed via PayOS with `payos_order_code`) and internal coin ledger movements (practice interview start fees, interview slot funding, avatar generation fees, avatar capacity purchases, and unused slot refunds upon terminal Job Posting close). Contains fields such as `from`, `to`, `amount`, `currency`, `description`, `status`, and `payos_order_code`. PayOS is the confirmed real-money payment provider. The platform does NOT maintain separate `payment_orders` and `coin_transactions` tables.

### Interview Slot (`job_postings.interview_slot`)
The frozen ERD field defining maximum recruitment interview capacity (the maximum number of Candidates that may be approved to proceed into the recruitment interview stage). One funded slot is consumed when an eligible applicant starts their first technical interview. Resuming that same session does not consume another slot. Eligible unused slots are refunded as coins upon terminal Job Posting auto-close. `interview_slot` does NOT represent total Applications/CVs submitted, company hiring headcount, or final hires. RoleCue does NOT persist or enforce company hiring headcount.

### Avatar Inventory Slot
A unit of storage capacity in a Candidate's or Recruiter's personal 3D avatar inventory. Does **not** represent generation credits. When the inventory is full, a user must delete an existing model or purchase an additional inventory slot using coins to store a new avatar.

### Close Intake
An operational action executed manually by a Recruiter (*Close Job Posting Intake*) or automatically by the System Handler (*Close Job Posting When Meet Configured Limitation* upon reaching `interview_slot` approved candidates). Halts receiving new Applications/CVs, automatically rejects all remaining unscreened or not-approved Applications, and preserves interview eligibility for already-approved Candidates within their 24-hour windows. Close Intake is NOT terminal Job Posting close and does NOT refund unused interview capacity. There is NO Reopen Intake flow and NO manual End Recruitment command.

### Terminal Job Posting Close (Formerly End Recruitment)
The automated conclusion of a Job Posting, reached automatically when EVERY Application in scope has a terminal result (`APPROVED` or `REJECTED`). Automatically executes `Refund unused JP Candidate Slot`, refunding all still-unused recruitment interview capacity to the owning Recruiter's personal wallet as internal coins. There is NO manual End Recruitment command. Close Intake does NOT trigger refunds.

### Voice Profile
A speech synthesis persona sourced from Text-to-Speech (TTS) providers. Encapsulates provider identifiers, language, accent, gender, and vocal tone parameters. Sourced from TTS providers and managed exclusively by the **Admin**.

### Personal 3D Avatar
A customized 3D humanoid avatar created through RoleCue's embedded free Avaturn iframe experience. Avaturn handles photos, validation, preview, customization, and final GLB generation. RoleCue receives the GLB, converts it to VRM, persists the VRM production asset, and associates it with the owning Candidate or Recruiter in their avatar inventory. A generation fee in coins is charged only upon successful VRM persistence in RoleCue.

### Membership Subscription (Superseded)
*Status: Superseded by Coin Wallets.* Formerly described recurring subscriptions and membership renewals. Completely replaced by coin packages, personal wallets, and slot funding.

### Core Technical Competencies
The evaluation dimensions used to score technical interview performance (`metrics`). Evaluated against the session's assigned questions and configured weights (`metrics_percentage`), with scores persisted in `score_details`.

### Performance Report
The comprehensive evaluation summary generated upon completion of an Interview Session. Persisted directly across `interviews.score`, `interviews.feedback`, `score_details (interview_id, metric_id, score)`, and `conversation_turns.feedback`. Displays an overall score (0–100), competency breakdowns, radar chart, turn-by-turn question reviews with model answers, and a prioritized study roadmap.

### Blend-Shape Visemes
Facial morph target blend-shapes corresponding to phonemic sounds, used to animate the virtual interviewer's mouth in real-time synchronization with TTS audio.

### 2D Waveform Fallback Mode
A performance-resilient fallback interface displaying an animated audio waveform in place of the 3D canvas when client hardware lacks GPU acceleration or WebGL support, preserving uninterrupted voice interaction.
