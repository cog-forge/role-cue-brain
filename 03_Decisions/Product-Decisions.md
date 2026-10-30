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
* **Rule:** Candidates **never** view, edit, or confirm an Interview Blueprint. There is no candidate blueprint preview capability. Recruiters **can** view and edit core questions within the Blueprint generated for their own Job Postings while the Posting is `pending` or `rejected`; the bank is locked once the Posting is `open` (Decision 13). Evaluation configuration and weights are handled separately.

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
* **Rule:** Maintain single canonical naming: `Job Posting`. Recruiter creation begins from JD-like content and confirms AI-extracted structured requirements. Before submitting for Admin approval, the Recruiter prepares the core-question bank, valid evaluation settings (custom or defaults), and locked company 3D interviewer model and Voice Profile. Public discovery and applications require moderation approval, successful coin funding, and Recruiter-opened intake, in that order (Decision 13).

---

## 4. Recruiter Workflow Stops at Application Approve / Reject (CV-First Flow)
* **Decision:** The recruitment workflow follows a CV-first sequence and strictly terminates at **Application Approve** or **Reject**.
* **Rationale:** RoleCue is an interview simulation platform and lightweight career matching board, not a full applicant tracking suite. Modeling multi-stage hiring pipelines, panel scheduling, or onboarding would dilute team focus and explode project complexity.
* **Rule:**
  1. While Job Posting intake is OPEN, Candidates may submit an unlimited number of Applications with CV/resume (not capped by `interview_slot`).
  2. The Application and CV are **immediately visible** to the owning Recruiter inside **View Application Detail** prior to any interview occurring.
  3. The Recruiter renders a **CV Screening Decision** (`Pass` or `Reject` CV screening) for at most `interview_slot` Candidates. Passing grants interview eligibility; it is not final hiring.
  4. Each approved application receives an individual deadline: `interview_deadline = approval_time + 24 hours` (`approval_time` is conceptual/event-time only; only `applications.interview_deadline` is persisted). A Candidate must complete the recruitment interview before the deadline; merely starting the interview before the deadline is insufficient. If the interview is not completed before the deadline, the System Handler marks the application as terminal `REJECTED`.
  5. An approved applicant conducts the technical interview; interview slot capacity is consumed upon the Candidate's **first successful interview start**, when the Application's single recruitment session is created (reconnects/resumes are free).
  6. The Interview Result and recruitment recordings/transcripts are attached to the Application.
  7. Recruiter final review decisions are **HARD-GATED until MAX(interview_deadline)** across all interview-eligible applications for that posting.
  8. Once the hard gate opens, the Recruiter reviews candidate profile, CV, evaluation score, and authorized recordings/transcripts consolidated directly inside **View Application Detail** and renders the final **Approve / Reject Application** decision.
  9. Once intake is closed and every application reaches a terminal state (`APPROVED` or `REJECTED`), or the Posting has no applications, the Job Posting reaches terminal auto-close, triggering a one-time automated internal coin refund of unused slot capacity (`Refund unused JP Candidate Slot`). Scope strictly terminates at this point.

---

## 5. RoleCue is NOT a Full ATS
* **Decision:** RoleCue explicitly disclaims full Applicant Tracking System (ATS) functionality.
* **Rationale:** Full ATS platforms require complex compliance workflows, candidate ranking algorithms, integration with HRIS systems, offer letter workflows, and background check integrations. RoleCue deliberately excludes these.
* **Rule:** RoleCue does not rank applicants for employers, automate hiring decisions, or manage hiring funnels. Scores are structured evidence for human review, not automated decisions.

---

## 6. Configure Interview Session is a Composite Capability
* **Decision:** `Configure Interview Session` is modeled as a single composite capability for a Candidate's Target JD practice interview, encompassing an available system 3D interviewer or eligible model from avatar inventory, an available Voice Profile, 3D room environment, difficulty, and duration.
* **Rationale:** Breaking visual, vocal, environmental, and difficulty settings into separate top-level use cases adds unnecessary administrative overhead. The user configures their session in a cohesive wizard.
* **Rule:** Interview configuration is a unified setup step. An interview originating from a Recruiter Job Posting instead uses that Job Posting's company-defined 3D interviewer model and Voice Profile, which the Candidate cannot override; a recruitment start that names its own avatar or voice is refused.
  * **Practice Avatar & Voice Eligibility:** A practice session may use only a system 3D interviewer or an avatar in the Candidate's **own** inventory; another user's personal avatar is their likeness and is never usable. The selected Voice Profile must be active (`voices.is_active`). No system avatar presets are currently persisted, so in practice the Candidate's own inventory avatars are the eligible models.

---

## 7. Personal 3D Avatar Inventory (Both Roles, Storage Slots Only)
* **Decision:** Both Candidates and Recruiters maintain personal avatar inventories created via the embedded free Avaturn iframe experience and persisted as standardized VRM assets. Ratified on **2026-10-20**: every Candidate and Recruiter starts with **3 personal avatar storage slots**.
* **Rationale:** Avaturn provides a polished web capture experience without requiring proprietary backend reconstruction pipelines. Charging only for storage capacity (never for creation itself) keeps avatar monetization simple and transparent.
* **(Settled 2026-10-27: no separate generation fee; supersedes the 2026-10-20 capacity-vs-fee wording.)**
* **Rule:**
  * Candidates and Recruiters possess personal avatar inventories; Administrators do **not**.
  * Avatar slots represent **storage capacity**: each saved avatar occupies one slot. At capacity, users delete existing models or purchase additional capacity slots using coins.
  * **Initial Capacity:** Every Candidate and Recruiter starts with 3 storage slots. Creating an avatar is free while a slot is available; when the inventory is full, creation is refused (`INVENTORY_FULL`) until the user deletes an avatar or buys more slots. Effectively, an avatar beyond the free capacity costs 1 coin (the slot it needs). The frontend reads actual capacity from the authoritative API because purchases can increase it.
  * **Capacity Price (Settled 2026-10-27):** One additional avatar storage slot costs **1 coin**, purchased 1–10 slots at a time (`POST /inventory/purchase`) and debited atomically through the shared ledger. This is a fixed system constant for now, not configuration. Purchased slots are permanent capacity; deleting an avatar frees its slot.
  * **No Generation Fee (Settled 2026-10-27):** There is **no** separate avatar generation fee. Avaturn capture, GLB generation, GLB-to-VRM conversion, and VRM persistence cost nothing as long as the user has a free storage slot; the only avatar charge is a storage slot purchase.
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
* **Decision:** Recruiter capabilities include managing intake (Open Intake by successful funding, Close Intake), funding interview capacity (`interview_slot`), editing own question banks and evaluation weights, screening CVs, and reviewing recruitment recordings and transcripts consolidated within View Application Detail.
* **Rationale:** Recruiters require operational control over interview capacity, application intake pacing, and candidate performance evidence without expanding into a full ATS.
* **Rule:**
  * **Moderation → Funding → Open Intake (Ratified 2026-10-20):** Recruiter prepares the Posting, confirms requirements, prepares the core-question bank, valid evaluation settings (Decision 13a), and company presentation, then submits for Admin moderation. Admin approval is a moderation decision. After approval, Recruiter funds `interview_slot` using wallet coins (1 coin per slot, exactly once, through the shared ledger); successful funding **is** the Recruiter's Open Intake action. Candidates may discover and apply only once the Posting is both approved and funded; approval alone does **not** open intake (the mapping and current implementation state are recorded below).
  * **Persistence Mapping (Settled 2026-10-29):** No schema change. The `job_posting_status` enum keeps only `pending`, `open`, `intake_closed`, `closed`, and `rejected`; no `approved` value or independent `intake_status` column is added. Admin approval sets `status = 'open'`. A Posting with `status = 'open' AND interview_slot IS NULL` is **approved but not yet open**: it is not listed on the job board, its public detail is not reachable, and applications are refused (`INTAKE_NOT_OPEN`); the owning Recruiter and Admin still see it as approved, awaiting funding. Successful funding is the Recruiter's Open Intake action: setting `interview_slot` opens intake in the same step, and there is no separate Open Intake endpoint or button. Intake is open exactly when `status = 'open' AND interview_slot IS NOT NULL` (see [[02_System/Domain-Model|Domain Model]]). Do not relax the funding gate.
  * **Implementation Aligned (2026-10-29, PR #73):** The interim behavior recorded on 2026-10-27, where Admin approval opened intake before funding, is closed. The job board, public detail and apply now require funded capacity, matching the settled mapping above. CV approval and recruitment interview start also require funded capacity, and funding is accepted only while the Posting is `open`.
  * **Question Bank Preparation & Submit for Review:** A new Posting is `pending` and in preparation. After confirming requirements, the Recruiter generates the core-question bank **once** (a failed generation may be retried; a second generation on an existing bank is refused), then reviews and edits it while the Posting is `pending` or `rejected`. Generating does not submit: the Recruiter explicitly **submits for review**, which requires a non-empty bank (`job_postings.review_submitted` marks a submitted Posting). Admin approves or rejects only submitted Postings. Deleting the last question withdraws the submission. A `rejected` Posting may regenerate and edit its bank, then resubmits. The bank is locked from `open` onward, and bank edits are serialized with Admin approval so an approval never opens a Posting with an empty bank.
  * **Atomic, Idempotent Funding:** Recruiter wallet coin debit, unified `transactions` ledger entry, and `job_postings.interview_slot` update must commit atomically and idempotently. Failed operations leave no partial debit/capacity; retries or duplicates cannot double-charge or allocate capacity twice. Internal funding never invokes PayOS.
  * **Funded Exactly Once (No Top-Ups):** Interview capacity is funded exactly once per Posting, at **1 coin per slot**. Repeating the funding with the same slot count returns the current capacity without charging; a request with a different count is refused. Capacity cannot be topped up later.
  * **Intake Control:** Open intake receives unlimited applications; Close intake (`Close Job Posting Intake` or automated `Close Job Posting When Meet Configured Limitation` when approved count reaches `interview_slot`) permanently closes intake and automatically marks all unscreened/unapproved applications as terminal `REJECTED`. Candidates who already passed CV screening can still complete their interviews within their 24-hour deadline (`interview_deadline = approval_time + 24 hours`, where `approval_time` is conceptual). Closing intake itself does **not** refund slots.
  * **Close Intake Chaining & Repeat Calls:** Closing intake then evaluates terminal close in the same operation. When no Application is still in progress (including a Posting with zero Applications), the Posting goes straight to terminal `closed`, and unused-slot refunds come from that terminal-close step. Calling Close Intake again on an `intake_closed` or `closed` Posting returns success without change.
  * **No Reopen Intake Flow:** There is **NO Reopen Intake** flow. Once closed, intake cannot be reopened.
  * **No Manual End Recruitment Flow:** There is **NO manual End Recruitment** capability.
  * **Terminal Auto-Close & Unused Slot Refund:** Once intake is closed, terminal Job Posting close is reached automatically when EVERY application in scope has a terminal result (`APPROVED` or `REJECTED`), or immediately when there are no applications. Upon terminal auto-close, the System Handler executes `Refund unused JP Candidate Slot`, refunding still-unused capacity (`interview_slot` minus consumed recruitment sessions, at 1 coin per slot) to the Recruiter's wallet as internal coins. The refund runs exactly once per Posting, on the transition to `closed`; an unfunded Posting refunds nothing.
  * **Consolidated Application Review:** Review of candidate profile, CV, evaluation score, and authorized recordings/transcripts is consolidated directly within **View Application Detail**. There are no separate top-level review use cases.
  * **Hard-Gated Final Decisions:** Final decisions (`Approve / Reject Application`) are **HARD-GATED until MAX(interview_deadline)** across all interview-eligible applications for that posting, evaluated on the database clock. Only an `interviewed` application can receive a final decision; each interview-eligible application whose own deadline passes without a completed interview is auto-rejected.
  * **Archive Job Posting (Removed):** `Archive Job Posting` is **not** part of RoleCue's current product contract. Terminology from earlier drafts referring to archiving Job Postings is obsolete.

---

## 13a. Per-Posting Evaluation Weights & Equal-Split Defaults (KAN-184)
* **Decision:** Ratified on **2026-10-18**: each Job Posting has its own Evaluation Weights, independent of the Core Question Bank, with equal-split defaults when the Recruiter has not saved a custom configuration.
* **Rationale:** A valid fallback permits approval without mandatory customization; locking weights once a Posting opens keeps Candidate evaluations consistent.
* **Rules:**
  * **Metrics Catalog:** The system manages a Metrics Catalog currently seeded with Technical Accuracy, Depth of Understanding, Problem-Solving & Approach, Answer Relevance, and Communication Clarity. These five metrics are the current baseline, not an immutable platform list.
  * **Custom Weights:** Recruiters may select a subset of metrics and allocate weights totaling **100%** for each Job Posting.
  * **Equal-Split Fallback:** Without a saved custom configuration, divide the total **100%** equally among the metrics currently available (five metrics means **20% each**). Compute these defaults on read; do not persist them in `metrics_percentage`.
  * **API Semantics:** When using defaults, return `configured: false` and `using_defaults: true`. This indicates a valid fallback totaling **100%**, not zero effective weight.
  * **Approval:** Admin may approve a Posting without Recruiter-customized weights because the fallback provides a valid evaluation configuration.
  * **Editing Boundary:** Recruiters may edit weights while the Posting is `pending` or `rejected`. Weights are locked in `open`, `intake_closed`, and `closed`. Because Admin approval sets `open` (Decision 13 persistence mapping, settled 2026-10-29), weights are locked from approval onward, including while an approved Posting awaits funding; no additional editable state is implied.
  * **Scoring Consistency:** KAN-61 scoring must use the same equal-split fallback when custom weights are absent.

---

## 13b. Locked Recruiter Visibility on the Candidate Job Board (KAN-187)
* **Decision:** Ratified on **2026-10-18**: the Candidate Job Board displays only Job Postings with `status = open` whose owning Recruiter is not under an effective account lock at query time. The moderation, funding, and Recruiter-opened intake prerequisites in Decision 13 also apply; `open` alone does not prove those gates were satisfied by current code.
* **Rationale:** Board visibility must reflect current account access restrictions while preserving existing recruitment data and lifecycle policies.
* **Rules:**
  * **Effective Lock:** A Recruiter is locked when `is_locked = true AND (lock_expires_at IS NULL OR lock_expires_at > NOW())`.
  * **List and Detail:** Apply the same visibility policy to the list and detail page; a hidden Posting's detail URL returns **404**.
  * **Automatic Visibility:** After Admin unlock or lock expiry, Postings whose status remains `open` automatically become visible again when Decision 13's publication prerequisites remain satisfied.
  * **No Lifecycle Side Effects:** Account lock does not change Job Posting status, delete Applications, or trigger refunds. Preserve existing Applications and Interviews; their handling remains governed by separate lifecycle policies.

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
  * **Unified Transactions Model:** Platform utilizes a single unified `transactions` table with fields `from`, `to`, `amount`, `currency`, `description`, `status`, `payos_order_code`, `created_at`. The proposed redesign into split `payment_orders` and `coin_transactions` was formally rejected by the team and represents accepted technical debt for this capstone. PayOS is the confirmed provider.
  * **Ledger Semantics:** `currency` is `VND` for real-money PayOS top-ups and `COIN` for internal coin movements. `from` and `to` hold the user identifier or the literal `system` counterparty; `amount` is an integer. `created_at timestamptz` (NOT NULL, database default, indexed) dates each row; it was added in migration 000014 (KAN-191/192/206), and rows written before it carry the migration time (dev data only).
  * **Revenue Definition:** Platform revenue is the sum of the `VND` amounts of **successful** PayOS top-ups. Internal coin movements (practice fees, interview slot funding, avatar storage slot purchases, refunds) are **not** revenue. Date-range reporting filters `created_at` over a `[date_from, date_to)` window.
  * **Reporting Time Zone (Settled 2026-10-27):** Admin billing screens, presets, custom ranges, and the CSV export use Vietnam time (UTC+07:00); a custom range covers inclusive calendar days in Vietnam time. APIs return `created_at` in UTC. A user's own wallet history shows times in the viewer's local time, newest first.
  * **Billing Derivation (Settled 2026-10-27):** No SQL view for the admin billing derivation, because a view would force a migration for every change to how revenue is derived. Each admin billing query keeps its own enrichment CTE with the date predicate; the repeated CTE stays open as tech debt (T9).
  * **Fixed Prices (Settled 2026-10-27):** One interview slot costs **1 coin**, one avatar storage slot costs **1 coin**, and one practice session costs **5 coins**. These are fixed system constants for now, not configuration.
  * **External vs. Internal:** External payment gateway (PayOS) handles real-money checkout to acquire coin packages. Internal spending (practice interview start fees, interview slot funding, avatar storage slot purchases) and internal refunds (`Refund unused JP Candidate Slot` at terminal auto-close) are internal database ledger operations that do not invoke PayOS.
  * **Start Charging Boundary:** Practice interviews are debited in coins (5 coins) from the Candidate's wallet upon session start (not completion); reconnecting to or resuming the same active session incurs no second charge. Starting again while the Candidate has an unfinished practice session for the same Role Profile returns that session instead of charging again, and a retried start carrying the same idempotency key returns the session it created.
  * **Slot Consumption Boundary:** Recruitment interviews consume one prepaid Recruiter slot upon Candidate's first successful interview start, when the Application's single recruitment session is created; resuming that session consumes no additional slot; Candidates are never charged. Consumption is counted from recruitment sessions, so the terminal-close refund never double-debits or double-credits.
  * **Avatar Charging Boundary:** Avatar creation is free while a storage slot is available; the only avatar charge is buying storage slots at 1 coin per slot, debited atomically through the ledger. There is no separate generation fee (Settled 2026-10-27).
  * **Terminal Auto-Close Refund:** Only terminal Job Posting auto-close refunds eligible unused interview slots as coins back to the Recruiter's wallet (`Refund unused JP Candidate Slot`), once per Posting. Close intake itself does not refund slots; when it chains straight into terminal close, the refund comes from the terminal-close step.

---

## 16. Unresolved Product Decisions & Scope Exclusions

The following entries distinguish ratified scope from unresolved implementation details and remaining exclusions. Unresolved parameters must **not** be silently filled with invented defaults:

1. **Commercial Parameters & Precision:**
   * Exact monetary pricing for coin packages.
   * Coin exchange ratios, currency precision (integer vs. decimal), and package sizes.
   * *(Settled 2026-10-27 and no longer open: interview slot 1 coin, avatar storage slot 1 coin, practice session 5 coins; Decision 15. There is no separate avatar generation fee; Decision 7.)*
   * *(Settled 2026-10-27 and no longer open: date-range revenue reporting filters `transactions.created_at`, added in migration 000014 (KAN-191/192/206); Decision 15.)*
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
   * *(Settled: Recruiters edit the question bank only while the Posting is `pending` or `rejected`; it is locked during active recruitment; Decision 13.)*
4. **Evaluation Dimensions & Formulas:**
   * Detailed definitions and criteria for each metric; the current Metrics Catalog baseline is recorded in Decision 13a.
   * Scoring formulas, passing/failing thresholds, evaluation AI policy, and validation rules or constraints not settled in Decision 13a.
5. **Recruitment Recording Retention & Post-Recruitment Policies:**
   * Audio/video recording retention periods and automated lifecycle purge schedules.
   * Physical cloud storage architecture and candidate recording consent workflows.
   * Post-recruitment transcript access policies (whether transcripts remain permanently restricted or unlock upon recruitment termination).
   * Session recovery timeout window (older conflicting reports cite 10 minutes vs. 15 minutes; unresolved).
6. **Admin Interview Behavior Guidelines & Remaining Administrative Exclusions:**
   * **Ratified on 2026-10-20:** Admin **CAN Configure Interview Behavior Guidelines**. This registered Capstone capability is mandatory functional scope. It requires Admin authorization and influences the intended behavior of the AI interviewer; completed historical interview results must remain unchanged.
   * This requirement does not establish that the capability is implemented in application `main`. Exact configuration UI, schema, persistence mechanism, guideline fields, versioning behavior, and prompt composition algorithm remain **TBD**, pending specification and review.
   * Unrestricted raw LLM system-prompt editing, global evaluation rubric/weight editing, runtime feature toggles, LLM provider configuration management, and coin package pricing administration remain excluded unless separately ratified. These exclusions do **not** exclude the registered Interview Behavior Guidelines capability.
   * **Frozen Use Case Gap:** The capability is absent from the active Version 2 Use Case diagram checked on 2026-10-20. Preserve the declared **62 counted Use Cases** and existing IDs; the documentation team will reconcile the formal diagram/SRS separately. See [[00_Project/Actors-and-Capabilities|Actors and Capabilities]] for inspection evidence and counting limitations.
7. **Approval, Funding & Intake Implementation Mapping (Settled 2026-10-29):**
   * Settled by the product owner without a schema change: approval sets `open`; `open` with `interview_slot IS NULL` is approved but not yet open; successful funding opens intake. See Decision 13 (*Persistence Mapping*). Implementation alignment is in progress; until it lands, the 2026-10-27 interim behavior (Candidates may apply before funding) still applies.
   * The mapping preserves Decision 13a's evaluation editing boundary and Decision 13b's visibility restrictions.

---

## 17. Standalone Better Auth Service (Single Session Authority)
* **Decision:** Authorized on **2026-10-20** and shipped: Better Auth runs in RoleCue's own independently deployable authentication service, separate from the Next.js frontend and the Go API.
* **Rationale:** Separating the authentication runtime from the web frontend gives it an independent deployment boundary without changing who owns identity, sessions, or business authorization.
* **Rule:**
  * Better Auth remains the **single session authority**: one Better Auth-owned identity, credential, and session model in the existing RoleCue database. "Single session" does not mean one session per user; a user may hold several concurrent sessions.
  * The web origin routes `/api/auth/*` directly to the auth service. Go verifies each session against the auth service and independently enforces business authorization (roles, verified email, ownership, account locks).
  * Cookie semantics and security boundaries, email verification, password recovery, OAuth, and two-factor behavior are preserved.
  * No secondary session model, JWT exchange, custom Go refresh tokens, new identity database, or replacement framework is authorized. See [[01_Domains/Auth/README|Auth Domain]].
