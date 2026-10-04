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
> * **`Job Posting` = The Company's Job Description.** It is created, updated, and archived by a **Recruiter** to advertise an open position at an employer, then submitted for Admin approval before public availability. Candidates browse approved Job Postings and submit Applications to them.
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
The first-class persistent **core-question bank** generated following human confirmation of extracted requirements, skills, and seniority context. Exactly **one current Blueprint** exists per JD (absent until generated). It defines ONLY the persistent/current core-question bank/list. It does NOT contain grading rubrics, evaluation criteria, competency weights, depth benchmarks, or evaluation matrices.
* **Evaluation Configuration Separation:** Evaluation criteria, competency weights, and scoring settings are a separate concern from the question bank Blueprint. Recruiters can configure posting-level evaluation weights on their Job Postings separately.
* **Access Boundary:** Strictly **internal and hidden from the Candidate**. Candidates never view, edit, or directly confirm an Interview Blueprint. Recruiters **can** view and edit core questions within the Blueprint generated for their own Job Postings.

### Interview Session
A single concrete execution attempt of an interview executing from an Interview Blueprint. It may originate from a Candidate Target JD (practice) or a Recruiter Job Posting (recruitment). Tracks real-time conversational turns, candidate speech transcripts, Question delivery, audio playback, and (for recruitment) video/audio recordings. Holds an immutable snapshot preserving the historical question-bank context and evaluation configuration used.

### Question
The generic runtime unit spoken by the 3D interviewer during an Interview Session. An interview conducts a random selection of $x$ core questions from the question bank Blueprint, with bounded follow-ups determined by candidate answers and interview context. Deciding whether to follow up and generating follow-up wording are decoupled responsibilities without premature vendor lock-in.

### Blueprint Snapshot / Session Historical Context
An immutable snapshot captured at the moment an Interview Session is initialized. Preserves the exact question-bank/question selection context AND the evaluation configuration actually used by the historical session. Ensures that historical evaluations, scoring, and performance reports remain 100% reproducible and tamper-proof even if the parent JD, question bank, or evaluation settings later evolve, without modeling evaluation criteria as part of the current Blueprint.

### Application
A candidate submission to a Recruiter's Job Posting containing Candidate application information and an uploaded CV/resume.
* **Immediate Visibility:** The Application and CV are immediately visible to the owning Recruiter upon submission, prior to any interview.
* **Lifecycle & Decisions:** Progresses through a two-decision lifecycle: initial `PENDING_CV_SCREENING` $\rightarrow$ Recruiter CV Screening Decision (`CV_PASSED` or `CV_REJECTED`) $\rightarrow$ (if passed, applicant conducts interview using a funded slot; Interview Result attached) $\rightarrow$ Recruiter Final Decision (`APPROVED` or `REJECTED`).

### CV Screening Decision
The Recruiter's initial evaluation of a candidate's uploaded CV/resume (`Pass` or `Reject` CV screening). Passing grants the candidate eligibility to conduct the required technical interview. It is distinct from and precedes the final application decision.

### Final Decision
The definitive, terminal decision rendered by the Recruiter (`Approve` or `Reject` Application) after reviewing the candidate's complete profile, CV, technical Interview Result, and recruitment audio/video recordings and transcripts. Marks the strict termination of recruitment scope.

### Interview Result
The persisted evaluation outcome (Performance Report) of the required technical interview associated with a Job Posting. Attached to the Application alongside recruitment recordings for Recruiter review. Candidates can view their recruitment score, but cannot view recruitment transcripts or recordings during recruitment.

### Coin
The internal digital unit of value on RoleCue used to fund platform capabilities. Purchased in coin packages via external payment gateways using real money. Coins are used to pay for Candidate practice interview starts, Recruiter interview slots, avatar generation fees, and additional avatar inventory capacity slots.

### Wallet (Coin Wallet)
A personal digital coin balance ledger held individually by a **Candidate** or **Recruiter**. Tracks current coin balance and ledger transactions. Administrators do **NOT** possess a wallet.

### Coin Transaction
An immutable internal financial ledger record representing an internal credit or debit of coins within a personal wallet (e.g., package purchase credit, practice interview debit, interview slot funding debit, avatar fee debit, avatar slot purchase debit, or unused slot refund credit upon End Recruitment). Internal coin movements are distinct from external payment gateway transactions.

### Payment Order (Payment Transaction)
An immutable record of an external real-money transaction processed through a third-party Payment Gateway to purchase a coin package. Verified cryptographically via signed webhooks.

### Interview Slot
A unit of prepaid interview capacity purchased by a Recruiter using coins from their wallet and allocated to an approved Job Posting. One slot is consumed when an eligible applicant starts the technical interview. Resuming that same session does not consume another slot. Eligible unused slots are refunded as coins upon End Recruitment. An interview slot represents interview capacity, not an application or CV review.

### Avatar Inventory Slot
A unit of storage capacity in a Candidate's or Recruiter's personal 3D avatar inventory. Does **not** represent generation credits. When the inventory is full, a user must delete an existing model or purchase an additional inventory slot using coins to store a new avatar.

### Close Intake
An operational action executed by a Recruiter to temporarily halt receiving new applications on an active Job Posting. Applicants who already passed CV screening prior to closing intake can still conduct their interview. Close intake does **not** refund interview slots or terminate recruitment. The Recruiter can reopen intake at any time.

### End Recruitment
The formal conclusion of recruitment for a Job Posting. Triggered by the Recruiter when hiring concludes or the vacancy is closed. Only ending recruitment calculates eligible unused funded interview slots and refunds them as coins back into the owning Recruiter's personal wallet.

### Voice Profile
A speech synthesis persona sourced from Text-to-Speech (TTS) providers. Encapsulates provider identifiers, language, accent, gender, and vocal tone parameters. Sourced from TTS providers and managed exclusively by the **Admin**.

### Personal 3D Avatar
A customized 3D humanoid avatar created through RoleCue's embedded free Avaturn iframe experience. Avaturn handles photos, validation, preview, customization, and final GLB generation. RoleCue receives the GLB, converts it to VRM, persists the VRM production asset, and associates it with the owning Candidate or Recruiter in their avatar inventory. A generation fee in coins is charged only upon successful VRM persistence in RoleCue.

### Membership Subscription (Superseded)
*Status: Superseded by Coin Wallets.* Formerly described recurring subscriptions and membership renewals. Completely replaced by coin packages, personal wallets, and slot funding.

### Core Technical Competencies
The evaluation dimensions used to score technical interview performance (Technical Accuracy, Depth of Understanding, Problem-Solving, Answer Relevance, Communication Clarity). Evaluated against the session's immutable blueprint snapshot and configured weights.

### Performance Report
The comprehensive evaluation artifact generated upon completion of an Interview Session. Includes an overall score (0–100), competency breakdowns, radar chart, turn-by-turn question reviews with model answers, and a prioritized study roadmap.

### Blend-Shape Visemes
Facial morph target blend-shapes corresponding to phonemic sounds, used to animate the virtual interviewer's mouth in real-time synchronization with TTS audio.

### 2D Waveform Fallback Mode
A performance-resilient fallback interface displaying an animated audio waveform in place of the 3D canvas when client hardware lacks GPU acceleration or WebGL support, preserving uninterrupted voice interaction.
