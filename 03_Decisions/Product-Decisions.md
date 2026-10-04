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
* **Decision:** The Interview Blueprint is an internal assessment plan and core-question bank.
* **Rationale:** Exposing the blueprint (with its question pool, benchmarks, and rubrics) to candidates would turn the mock interview into an artificial memorization exercise rather than an authentic, adaptive simulation. Conversely, Recruiters need agency to tailor interview content for their specific company openings.
* **Rule:** Candidates **never** view, edit, or confirm an Interview Blueprint. There is no candidate blueprint preview capability. Recruiters **can** view and edit core questions within the Blueprint generated for their own Job Postings.

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
  1. The Candidate submits an Application with an uploaded CV/resume.
  2. The Application and CV are **immediately visible** to the owning Recruiter prior to any interview occurring. (The former assumption requiring a completed interview prior to visibility is superseded).
  3. The Recruiter renders a **CV Screening Decision** (`Pass` or `Reject` CV screening). Passing grants interview eligibility; it is not final hiring.
  4. An approved applicant conducts the technical interview using a funded slot prepaid by the Recruiter.
  5. The Interview Result and recruitment recordings are attached to the Application.
  6. The Recruiter reviews the candidate dossier, evaluation result, and recordings/transcript, rendering a final **Approve** or **Reject** decision.
  7. Scope strictly terminates at this binary final verdict.

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

## 13. Recruiter Intake Management, Slot Funding & Recording Access
* **Decision:** Recruiter capabilities include managing intake (Open/Close), funding interview slots, editing own question banks and evaluation weights, screening CVs, and reviewing recruitment recordings.
* **Rationale:** Recruiters require operational control over interview capacity, application intake pacing, and candidate performance evidence without expanding into a full ATS.
* **Rule:**
  * **Intake Control:** Open intake receives new applications; Close intake temporarily stops new submissions. Candidates who already passed CV screening can still interview while intake is closed. Close intake does **not** refund slots.
  * **End Recruitment:** Formally finishes recruitment on a posting. Only ending recruitment refunds eligible unused interview slots as coins back into the Recruiter's personal wallet.
  * **Evaluation Settings:** Posting-level evaluation weights are configured by the Recruiter separately from the question bank Blueprint.
  * **Recruitment Recordings:** Audio/video recordings and transcripts are retained exclusively for the owning Recruiter. Candidates view their evaluation score, but cannot view recruitment transcripts or recordings during recruitment. Admin does not have recording access by inference.

---

## 14. Adaptive Question Loop with Decoupled Follow-Up Decisions (Jev is Unselected)
* **Decision:** The interview runtime selects a random set of $x$ core questions from the question bank Blueprint and may ask bounded follow-ups based on the candidate's answers and interview context.
* **Rationale:** Deciding whether to ask a follow-up and generating follow-up question wording are distinct responsibilities. Runtime execution must avoid rigid lock-in to an unverified external vendor or asserting that a single LLM owns all decisions.
* **Rule:**
  * The simulation samples a random set of $x$ core questions from the bank and conducts adaptive dialogue with bounded follow-ups.
  * **Jev/TypeSafe Status:** Jev was discussed orally and is **tentative and NOT selected**. Do not add a mandatory Jev decision-provider integration or assert that a specific vendor owns follow-up decisions.
  * The runtime question loop architecture must remain vendor-agnostic and decoupled from premature single-model or external decision engine selections.

---

## 15. Coin Wallets, Slot Funding, and Charging Boundaries
* **Decision:** Platform monetization is structured entirely around coin packages, personal wallets, and slot funding.
* **Rationale:** Replaces recurring memberships with a flexible, transparent pay-per-use and capacity-funding model for Candidates and Recruiters.
* **Rule:**
  * **Wallets:** Only Candidates and Recruiters hold personal coin wallets. Admin has no wallet.
  * **External vs. Internal:** External payment gateways handle real-money checkout to acquire coin packages. Internal spending (practice interview start fees, interview slot funding, avatar generation fees, avatar capacity purchases) and internal refunds (End Recruitment unused slot refunds) are internal database ledger operations that do not invoke the external gateway.
  * **Start Charging Boundary:** Practice interviews are debited in coins from the Candidate's wallet upon session start (not completion); reconnecting to or resuming the same active session incurs no second charge.
  * **Slot Consumption Boundary:** Recruitment interviews consume one prepaid Recruiter slot upon session start; resuming that session consumes no additional slot; Candidates are never charged.
  * **Avatar Charging Boundary:** The avatar generation fee is debited in coins only after successful VRM persistence in RoleCue.
  * **End Recruitment Refund:** Only End Recruitment refunds eligible unused interview slots as coins back to the Recruiter's wallet. Close intake does not refund slots.

---

## 16. Unresolved Product Decisions (Awaiting Explicit Confirmation)

The following operational, numerical, and architectural parameters remain unresolved product questions and must **not** be silently filled with invented defaults:

1. **Commercial Parameters & Precision:**
   * Exact monetary pricing for coin packages.
   * Coin exchange ratios, currency precision (integer vs. decimal), package sizes, and initial free avatar storage capacity.
2. **Interview Slot Policies:**
   * Slot reservation mechanics and concurrency limits during high application traffic.
   * Handling in-flight active interview sessions when a Recruiter triggers End Recruitment.
   * Exception/refund policies for technical interview disruptions.
   * Maximum permitted interview attempts per application.
3. **Question Bank Sampling & Runtime Policy:**
   * Exact core-question sample size $x$ per interview session.
   * Core-question selection/coverage algorithms and difficulty balancing.
   * Specific bounds and numerical limits on follow-up questions per core question.
   * Dedicated follow-up decision mechanism, vendor selection, and fallback behaviors.
   * Rules regarding whether and how Recruiters may edit question bank items during active recruitment.
4. **Evaluation Dimensions & Formulas:**
   * Finalized technical competency dimensions, criteria definitions, and default weight distribution.
   * Validation rules, constraints, and score passing/failing thresholds.
5. **Recruitment Recording Retention & Post-Recruitment Policies:**
   * Audio/video recording retention periods and automated lifecycle purge schedules.
   * Physical cloud storage architecture and candidate recording consent workflows.
   * Post-recruitment transcript access policies (whether transcripts remain permanently restricted or unlock upon recruitment termination).
   * Session recovery timeout window (older conflicting reports cite 10 minutes vs. 15 minutes; unresolved).
6. **Global Administrative Management:**
   * Whether platform Administrators have authority to manage global AI prompt templates and evaluation criteria.
   * Administrative management and editing of global coin package pricing tiers.
