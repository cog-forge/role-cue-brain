---
title: Foundational Product Decisions
tags:
  - decisions
  - product-decisions
  - architecture
  - invariants
aliases:
  - Product Decisions
  - Long-Lived Decisions
---

# Foundational Product Decisions

This document records the ratified, long-lived product decisions that govern the architecture, domain boundaries, and user experiences of RoleCue. These decisions serve as permanent interpretive guidelines for engineering and product design.

---

## 1. Blueprint Access Invariant: Hidden from Candidate, Recruiter-Editable
* **Decision:** The Interview Blueprint is ONLY the persistent/current core-question bank/list generated from confirmed skills, requirements, and seniority/refinement context. It contains exclusively the bank of core questions. It does NOT contain grading rubrics, evaluation criteria, competency weights, depth benchmarks, or evaluation matrices; evaluation configuration is a separate concern.
* **Rationale:** Exposing the blueprint (with its core-question pool) to candidates would turn the mock interview into an artificial memorization exercise rather than an authentic, adaptive simulation. Conversely, Recruiters need agency to tailor interview content for their specific company openings.
* **Rule:** Candidates **never** view, edit, or confirm an Interview Blueprint. There is no candidate blueprint preview capability. Recruiters **can** view and edit core questions within the Blueprint generated for their own Job Postings. Evaluation configuration and weights are handled separately.

---

## 2. Requirement Confirmation Precedes Blueprint Generation (Single Current Bank per JD)
* **Decision:** Human review and explicit confirmation of extracted technical competencies and refinement notes must occur **before** the system compiles the Interview Blueprint. Exactly **one current Blueprint** exists per JD.
* **Rationale:** The question-bank generator requires confirmed, human-verified requirements to build a coherent question bank. Generating multiple current banks creates state ambiguity and redundant storage.
* **Rule:**
  $$\text{Confirmed Requirements} + \text{Refinement Notes} \longrightarrow \text{Single Core-Question Bank Blueprint}$$
  A JD has at most one current Blueprint (absent until generated). Changing difficulty or session configuration does not generate multiple current banks.

---

## 3. Job Posting is the Recruiter's Company JD
* **Decision:** The `Job Posting` entity represents the employer's Job Description. A separate "Corporate JD" entity is **not** created.
* **Rationale:** Introducing both "Corporate JD" and "Job Posting" creates redundant entities and semantic ambiguity. A Recruiter's Job Posting serves as both the public job board opening and the underlying company JD.
* **Rule:** Maintain single canonical naming: `Job Posting`. Recruiter creation begins from JD-like content and confirms AI-extracted structured requirements. Before submitting for Admin approval, the Recruiter locks the company 3D interviewer model and Voice Profile. Only approved Job Postings are publicly available.

---

## 4. Recruiter Workflow Stops at Application Approve / Reject (CV-First Flow)
* **Decision:** The recruitment workflow follows a CV-first sequence and strictly terminates at **Application Approve** or **Reject**.
* **Rationale:** RoleCue is an interview simulation platform and lightweight career matching board, not a full applicant tracking suite. Modeling multi-stage hiring pipelines, panel scheduling, or onboarding would dilute team focus and explode project complexity.
* **Rule:**
  1. While Job Posting intake is OPEN, Candidates may submit an unlimited number of Applications with CV/resume (not capped by `interview_slot`).
  2. The Application and CV are **immediately visible** to the owning Recruiter inside **View Application Detail** prior to any interview occurring.
  3. The Recruiter renders a **CV Screening Decision** (`Pass` or `Reject` CV screening) for at most `interview_slot` Candidates. Passing grants interview eligibility; it is not final hiring.
  4. Each approved application receives an individual deadline: `interview_deadline = cv_approved_at + 24 hours`. If the Candidate does not start before the deadline, the System Handler marks the application as terminal `REJECTED`.
  5. An approved applicant conducts the technical interview; interview slot capacity is consumed upon the Candidate's **first successful interview start** (reconnects/resumes are free).
  6. The Interview Result and recruitment recordings/transcripts are attached to the Application.
  7. Recruiter final review decisions are **HARD-GATED until MAX(interview_deadline)** across all interview-eligible applications for that posting.
  8. Once the hard gate opens, the Recruiter reviews candidate profile, CV, evaluation score, and authorized recordings/transcripts consolidated directly inside **View Application Detail** and renders the final **Approve / Reject Application** decision.
  9. When every application reaches a terminal state (`APPROVED` or `REJECTED`), the Job Posting reaches terminal auto-close, triggering automated internal coin refund of unused slot capacity (`Refund unused JP Candidate Slot`). Scope strictly terminates at this point.

---

## 5. RoleCue is NOT a Full ATS
* **Decision:** RoleCue explicitly disclaims full Applicant Tracking System (ATS) functionality.
* **Rationale:** Full ATS platforms require complex compliance workflows, candidate ranking algorithms, integration with HRIS systems, offer letter workflows, and background check integrations. RoleCue deliberately excludes these.
* **Rule:** RoleCue does not rank applicants for employers, automate hiring decisions, or manage hiring funnels. Scores are structured evidence for human review, not automated decisions.

---

## 6. Configure Interview Session is a Composite Capability
* **Decision:** `Configure Interview Session` is modeled as a single composite capability for a Candidate's Target JD practice interview, encompassing an available system 3D interviewer or eligible model from avatar inventory, an available Voice Profile, 3D room environment, difficulty, and duration.
* **Rationale:** Breaking visual, vocal, environmental, and difficulty settings into separate top-level use cases adds unnecessary administrative overhead. The user configures their session in a cohesive wizard.
* **Rule:** Interview configuration is a unified setup step. An interview originating from a Recruiter Job Posting instead uses that Job Posting's company-defined 3D interviewer model and Voice Profile, which the Candidate cannot override.

---

## 7. Personal 3D Avatar Inventory (Both Roles, Capacity vs. Generation Fee)
* **Decision:** Both Candidates and Recruiters maintain personal avatar inventories created via the embedded free Avaturn iframe experience and persisted as standardized VRM assets.
* **Rationale:** Avaturn provides a polished web capture experience without requiring proprietary backend reconstruction pipelines. Separating storage capacity from generation fees allows transparent monetization.
* **Rule:**
  * Candidates and Recruiters possess personal avatar inventories; Administrators do **not**.
  * Avatar slots represent **storage capacity**, not generation credits. At capacity, users delete existing models or purchase additional capacity slots using coins.
  * **Charging Boundary:** The avatar generation fee is debited in coins **only after successful VRM persistence in RoleCue**. No fee is charged for opening the studio, uploading photos, generating an Avaturn GLB, or starting conversion.
  * RoleCue embeds the free Avaturn iframe; Avaturn generates the final GLB; RoleCue converts it to VRM and persists it. No Avaturn Pro backend API, marketplace, or admin catalog is supported.

---

## 8. Admin Manages Voice Profiles, but NOT 3D Avatars or Environments
* **Decision:** The Administrator manages the catalog of Voice Profiles sourced from TTS providers, but does **NOT** manage the 3D avatar catalog or 3D background scenes.
* **Rationale:** 3D avatar meshes and WebGL environment scenes require 3D modeling, vertex optimization, and collision rigging, making ad-hoc administrative uploads impractical. Conversely, TTS voice profiles simply reference provider API identifiers and string metadata, which are safe and practical for admin curation.
* **Rule:** 3D avatars and environments are built-in presets; Voice Profiles are admin-governed. Admin does not possess a personal avatar inventory or personal coin wallet.

---

## 9. No Multi-Tenancy Architecture
* **Decision:** RoleCue operates on a single relational schema without multi-tenant architecture.
* **Rationale:** The platform does not require complex multi-tenant isolation (no tenant subdomains, tenant connection pools, tenant middleware, schema-per-tenant, or PostgreSQL Row-Level Security). Recruiter accounts manage Job Postings directly via relational foreign keys.
* **Rule:** Avoid multi-tenant complexity; use straightforward relational associations.

---

## 10. No 3D Marketplace or Community Publishing
* **Decision:** 3D avatar marketplace, trading, and community model publishing are strictly excluded from the product.
* **Rationale:** A 3D asset marketplace introduces asset moderation, copyright liability, 3D mesh security scanning, and creator payout systems that fall outside RoleCue's core mission.
* **Rule:** Avatars are restricted to curated platform presets and user-generated personal avatars stored in Candidate or Recruiter inventories.

---

## 11. Guest Capabilities Strictly Limited to Landing & Registration
* **Decision:** Guest capabilities are strictly limited to viewing the landing page and registering for an account.
* **Rationale:** Speculative public features (such as pricing plan exploration, public 3D avatar hero teasers, or anonymous 2-question voice demos) are excluded from the canonical scope to avoid unauthorized capability creep and unnecessary attack surfaces.
* **Rule:** Guests can only View Landing Page and Register.

---

## 12. Membership Subscriptions (SUPERSEDED)
* **Status:** **SUPERSEDED** on 2026-10-04 by Decision 15.
* **Historical Content:** Formerly specified that monetization was governed exclusively through Candidate Membership Subscriptions and renewals, without practice credits or wallets.
* **Superseded Rationale:** Project owner ratified the replacement of all memberships, renewals, and recurring subscriptions with coin wallets, coin packages, and slot funding.

---

## 13. Recruiter Intake Management, Capacity Limits & View Application Detail Consolidation
* **Decision:** Recruiter capabilities include managing intake (Close Intake), funding interview capacity (`interview_slot`), editing own question banks and evaluation weights, screening CVs, and reviewing recruitment recordings and transcripts consolidated within View Application Detail.
* **Rationale:** Recruiters require operational control over interview capacity, application intake pacing, and candidate performance evidence without expanding into a full ATS.
* **Rule:**
  * **Intake Control:** Open intake receives unlimited applications; Close intake (`Close Job Posting Intake` or automated `Close Job Posting When Meet Configured Limitation` when approved count reaches `interview_slot`) permanently closes intake and automatically marks all unscreened/unapproved applications as terminal `REJECTED`. Candidates who already passed CV screening can still interview within their 24-hour deadline (`cv_approved_at + 24 hours`). Close intake does **not** refund slots.
  * **No Reopen Intake Flow:** There is **NO Reopen Intake** flow. Once closed, intake cannot be reopened.
  * **No Manual End Recruitment Flow:** There is **NO manual End Recruitment** capability.
  * **Terminal Auto-Close & Unused Slot Refund:** Terminal Job Posting close is reached automatically when EVERY application in scope has a terminal result (`APPROVED` or `REJECTED`). Upon terminal auto-close, the System Handler executes `Refund unused JP Candidate Slot`, refunding still-unused capacity to the Recruiter's wallet as internal coins.
  * **Consolidated Application Review:** Review of candidate profile, CV, evaluation score, and authorized recordings/transcripts is consolidated directly within **View Application Detail**. There are no separate top-level review use cases.
  * **Hard-Gated Final Decisions:** Final decisions (`Approve / Reject Application`) are **HARD-GATED until MAX(interview_deadline)** across all interview-eligible applications for that posting.
  * **Archive Job Posting (Removed):** `Archive Job Posting` is **not** part of RoleCue's current product contract. Terminology from earlier drafts referring to archiving Job Postings is obsolete.

---

## 14. Adaptive Question Loop with Decoupled Follow-Up Decisions (Jev is Unselected)
* **Decision:** The interview runtime selects a random set of $x$ core questions from the question bank (`core_questions`, conceptual Blueprint) and may ask bounded follow-ups based on the candidate's answers and interview context.
* **Rationale:** Deciding whether to ask a follow-up and generating follow-up question wording are distinct responsibilities. Runtime execution must avoid rigid lock-in to an unverified external vendor or asserting that a single LLM owns all decisions.
* **Rule:**
  * The simulation samples a random set of $x$ core questions from the bank and conducts adaptive dialogue with bounded follow-ups.
  * **Jev/TypeSafe Status:** Jev was discussed orally and is **tentative and NOT selected**. Do not add a mandatory Jev decision-provider integration or assert that a specific vendor owns follow-up decisions.
  * The runtime question loop architecture must remain vendor-agnostic and decoupled from premature single-model or external decision engine selections.

---

## 15. Coin Wallets, Unified Transactions, Slot Capacity & Charging Boundaries
* **Decision:** Platform monetization is structured entirely around coin packages, personal wallets, unified transactions, and slot capacity.
* **Rationale:** Replaces recurring memberships with a flexible, transparent pay-per-use and capacity-funding model for Candidates and Recruiters.
* **Rule:**
  * **Wallets:** Only Candidates and Recruiters hold personal coin wallets. Admin has no wallet.
  * **Unified Transactions Model:** Platform utilizes a single unified `transactions` table with fields `from`, `to`, `amount`, `currency`, `description`, `status`, `payos_order_code`. The proposed redesign into split `payment_orders` and `coin_transactions` was formally rejected by the team and represents accepted technical debt for this capstone. PayOS is the confirmed provider.
  * **External vs. Internal:** External payment gateway (PayOS) handles real-money checkout to acquire coin packages. Internal spending (practice interview start fees, interview slot funding, avatar generation fees, avatar capacity purchases) and internal refunds (`Refund unused JP Candidate Slot` at terminal auto-close) are internal database ledger operations that do not invoke PayOS.
  * **Start Charging Boundary:** Practice interviews are debited in coins from the Candidate's wallet upon session start (not completion); reconnecting to or resuming the same active session incurs no second charge.
  * **Slot Consumption Boundary:** Recruitment interviews consume one prepaid Recruiter slot upon Candidate's first successful interview start; resuming that session consumes no additional slot; Candidates are never charged.
  * **Avatar Charging Boundary:** The avatar generation fee is debited in coins only after successful VRM persistence in RoleCue.
  * **Terminal Auto-Close Refund:** Only terminal Job Posting auto-close refunds eligible unused interview slots as coins back to the Recruiter's wallet (`Refund unused JP Candidate Slot`). Close intake does not refund slots.

---

## 16. Unresolved Product Decisions & Scope Exclusions

The following operational, numerical, and architectural parameters remain unresolved product questions or scope exclusions and must **not** be silently filled with invented defaults:

1. **Commercial Parameters & Precision:**
   * Exact monetary pricing for coin packages.
   * Coin exchange ratios, currency precision (integer vs. decimal), package sizes, and initial free avatar storage capacity.
2. **Interview Slot Policies:**
   * Slot reservation mechanics and concurrency limits during high application traffic.
   * Handling in-flight active interview sessions during terminal auto-close.
   * Exception/refund policies for technical interview disruptions.
   * Maximum permitted interview attempts per application.
3. **Question Bank Sampling & Runtime Policy:**
   * Exact core-question sample size $x$ per interview session.
   * Core-question selection/coverage algorithms and difficulty balancing.
   * Specific bounds and numerical limits on follow-up questions per core question.
   * Dedicated follow-up decision mechanism, vendor selection, and fallback behaviors.
   * Rules regarding whether and how Recruiters may edit question bank items during active recruitment.
4. **Evaluation Dimensions & Formulas:**
   * Finalized technical competency dimensions, criteria definitions, and default weight distribution (existing five dimensions remain an illustrative/baseline set only and are NOT an immutable platform contract).
   * Exact criteria, defaults, formulas, validation rules, constraints, and score passing/failing thresholds.
5. **Recruitment Recording Retention & Post-Recruitment Policies:**
   * Audio/video recording retention periods and automated lifecycle purge schedules.
   * Physical cloud storage architecture and candidate recording consent workflows.
   * Post-recruitment transcript access policies (whether transcripts remain permanently restricted or unlock upon recruitment termination).
   * Session recovery timeout window (older conflicting reports cite 10 minutes vs. 15 minutes; unresolved).
6. **Global Administrative Management (Excluded from Frozen Scope):**
   * Global AI prompt templates, global evaluation criteria editing, runtime interview feature toggles, and coin package pricing tier editing are excluded from the current frozen scope.
