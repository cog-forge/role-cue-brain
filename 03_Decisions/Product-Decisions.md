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
* **Decision:** The Interview Blueprint is ONLY the persistent/current core-question bank/list generated from confirmed skills, requirements, and seniority/refinement context. It identifies the pool of core questions, not an embedded rubric catalog or posting-weight matrix. Decision 24 adds a rubric_id and private expected_answer to each core question while storing reusable rubric definitions separately.
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
  * **Practice Avatar & Voice Eligibility:** A practice session may use only a system 3D interviewer or an avatar in the Candidate's **own** inventory; another user's private avatar cannot be selected directly. Decision 26 adds a purchased copy in the buyer's own inventory after implementation, without opening access to the seller's private avatar row. The selected Voice Profile must be active (`voices.is_active`). No system avatar presets are currently persisted, so in practice the Candidate's own inventory avatars are the eligible models.

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
  * **No Generation Fee (Settled 2026-10-27):** There is **no** separate avatar generation fee. Avaturn capture, GLB generation, GLB-to-VRM conversion, and VRM persistence cost nothing as long as the user has a free storage slot; creating an avatar has no generation charge. Decision 26 separately adds payment for an avatar listed by another user (implementation pending); capacity pricing is unchanged.
  * **Deleting an avatar deletes its stored model (Settled 2026-11-11, KAN-299, PR #128):** removing an avatar removes its `avatars` row and the model file in storage; a file that is already gone is tolerated and a storage failure is retried, so personal data is not left orphaned. Decision 26's shared assets will need their own deletion rule.
  * RoleCue embeds the free Avaturn iframe; Avaturn generates the final GLB; RoleCue converts it to VRM and persists it. No Avaturn Pro backend API or Admin-authored avatar catalog is supported. Decision 26 adds moderated user listings as approved future scope; it does not establish provider resale rights.

---

## 8. Admin Manages Voice Profiles, but NOT 3D Avatars or Environments
* **Decision:** The Administrator manages the catalog of Voice Profiles sourced from TTS providers, but does **NOT** manage the 3D avatar catalog or 3D background scenes.
* **Rationale:** 3D avatar meshes and WebGL environment scenes require 3D modeling, vertex optimization, and collision rigging, making ad-hoc administrative uploads impractical. Conversely, TTS voice profiles simply reference provider API identifiers and string metadata, which are safe and practical for admin curation.
* **Rule:** Admin does not author/upload 3D meshes or environments; Voice Profiles are admin-governed. Admin has no personal avatar inventory or wallet. Decision 26 adds moderation of user Marketplace listings, not an Admin-owned mesh catalog (implementation pending).

---

## 9. No Multi-Tenancy Architecture
* **Decision:** RoleCue operates on a single relational schema without multi-tenant architecture.
* **Rationale:** The platform does not require complex multi-tenant isolation (no tenant subdomains, tenant connection pools, tenant middleware, schema-per-tenant, or PostgreSQL Row-Level Security). Recruiter accounts manage Job Postings directly via relational foreign keys.
* **Rule:** Avoid multi-tenant complexity; use straightforward relational associations.

---

## 10. No 3D Marketplace or Community Publishing
* **Status:** SUPERSEDED on 2026-11-07 by Decision 26 for the agreed minimal, coin-only, moderated Avatar Marketplace. Preserve this entry as historical rationale, not a current exclusion.
* **Historical Decision:** Marketplace, community model trading and publishing were excluded to avoid moderation, redistribution and payout complexity.
* **Current Rule:** Decision 26 permits user listings and paid copies in own inventory; no cash-out, fee engine, per-buyer entitlement/revoke/refund, resale/gifting/NFT or structured purchase table. Implementation is pending; consult [[01_Domains/Avatar-Marketplace/Avatar-Marketplace-Spec|the approved spec]].

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
  * **Loop Policy (Settled 2026-11-03, KAN-56, PR #92):**
    * $x$ is `interview.question_limit` (default **8**, maximum 20), drawn as a random sample of the bank once at session start and frozen in `interview_questions`. A bank smaller than $x$ yields the whole bank; this is the implemented behavior pending product confirmation.
    * Follow-ups per core question are bounded by `INTERVIEW_FOLLOW_UP_LIMIT` (default **1**, allowed 0–3). `0` turns follow-ups off, and the interview then runs exactly as before.
    * Each follow-up decision is bounded by `INTERVIEW_FOLLOW_UP_TIMEOUT` (default **5 s**, allowed 1–15 s). A timeout, a provider error or an invalid model output skips the follow-up and moves to the next core question.
    * The decision seam is `FollowUpDecider`. The default adapter is one LLM call with a strict, validated JSON output and an injection-resistant prompt that treats the Candidate's answer as untrusted data. Jev remains unselected and could become another adapter.
    * If the connection drops during a follow-up, an unanswered follow-up is neither stored nor replayed; on reconnect the interviewer asks the next core question.

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
  * **Avatar Charging Boundary:** Avatar creation is free while a storage slot is available; capacity costs 1 coin per slot, debited atomically through the ledger. Decision 26 adds a separate coin payment to a seller for a listed avatar copy (implementation pending). There is no separate generation fee (Settled 2026-10-27).
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
   * Core-question coverage algorithms and difficulty balancing beyond the random sample.
   * Whether a bank smaller than $x$ is acceptable (today the whole bank is used).
   * A dedicated external follow-up decision vendor (Jev remains unselected; backlog KAN-274).
   * Question difficulty (easy / medium / hard) and duration-based question counts: moved back to the backlog on 2026-11-03 (KAN-216) for the team to revisit.
   * *(Settled 2026-11-03 and no longer open: $x$ = `interview.question_limit` 8, follow-up limit 1 (0–3), decision timeout 5 s, skip-on-failure fallback and the `FollowUpDecider` seam; Decision 14.)*
   * *(Settled: Recruiters edit the question bank only while the Posting is `pending` or `rejected`; it is locked during active recruitment; Decision 13.)*
4. **Evaluation Dimensions & Formulas:**
   * Metric catalog baseline remains in Decision 13a; rubric criteria may now reference it under Decision 24. Exact curated templates/scoring guides and domain calibration still require review.
   * Criterion-driven metric aggregation and the meaning of question/metric/overall scores are now settled by Decision 24. Recruitment passing/failing thresholds, calibration acceptance thresholds, partial-session policies and the engineering details flagged in the new spec remain to be confirmed. Practice Roadmap topic threshold (five distinct questions, mean >=80), reversible learning progress and delivered-skip/unasked rules are settled separately by Decision 25; they do not establish recruitment thresholds.
5. **Recruitment Recording Retention & Post-Recruitment Policies:**
   * *(Settled 2026-11-03 and no longer open: a recruitment recording is kept for **90 days after the Application's final decision** and then deleted, on the normal storage provider (`storage.Store`) like every other file. The deletion job is not built yet; it waits for the System Handler job runner (KAN-174).)*
   * Consent workflow: today the Candidate sees a mandatory disclosure in preflight and in the room's waiting screen, without a consent checkbox. Whether a required checkbox is needed is open.
   * Admin access to recordings.
   * Post-recruitment transcript access policies (whether transcripts remain permanently restricted or unlock upon recruitment termination).
   * *(Settled 2026-11-03 and no longer open: a session with no delivered question or stored answer for 15 minutes is abandoned; Decision 20.)*
6. **Admin Interview Behavior Guidelines & Remaining Administrative Exclusions:**
   * **Ratified on 2026-10-20:** Admin **CAN Configure Interview Behavior Guidelines**. This registered Capstone capability is mandatory functional scope. It requires Admin authorization and influences the intended behavior of the AI interviewer; completed historical interview results must remain unchanged.
   * *(Settled 2026-11-11 and no longer open: fields, persistence, versioning, application timing and prompt composition are specified by Decision 27 and implemented in `main` with an Admin screen at `/admin/interview-guidelines` (KAN-276, PR #128).)*
   * Unrestricted raw LLM system-prompt editing, administrative overrides of posting weights or historical evaluation results, runtime feature toggles, LLM provider configuration management, and coin package pricing administration remain excluded unless separately ratified. These exclusions do **not** exclude the registered Interview Behavior Guidelines capability.
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

---

## 18. Recruitment Interviews Are Recorded; Practice Is Not (Settled 2026-11-03, KAN-87, PR #91)
* **Decision:** A recruitment interview is recorded as one continuous camera and microphone recording for the owning Recruiter. A practice interview never records and never asks for the camera to record.
* **Rationale:** The recording is evidence for the Recruiter's final decision. A required recording must never silently disappear or be faked.
* **Rule:**
  * **Camera required:** the Candidate cannot start a recruitment interview until camera permission is granted and the recording is running. Preflight and the room show clear readiness and recovery copy.
  * **Upload failure does not cancel the interview:** the browser retries automatically up to 3 times, and the Candidate can retry from the room or the completion screen. Giving up or leaving marks the recording `failed`, so the Recruiter sees "recording failed". The system never silently drops to "no recording" and never fakes `ready`.
  * **Disclosure, not eKYC:** the copy says what is recorded, who can see it (only the Recruiter of this Job Posting), why (interview evidence), and that it is deleted 90 days after the hiring decision. It never mentions identity verification, cheating detection or AI video analysis.
  * The Candidate can see their own recording status (`GET /interviews/:id/recording`, recruitment only, status only) but cannot replay or export the recording.
  * Retention: Decision 16 item 5.

---

## 19. Bilingual UI, English Interview (Implemented 2026-11-02, KAN-220)
* **Decision:** The web UI ships in English (default) and Vietnamese, chosen by a flag switcher and remembered in the `NEXT_LOCALE` cookie. The interview conversation is always English.
* **Rule:**
  * Translated: all screen chrome for Guest, Candidate, Recruiter and Admin, including the interview room's status, controls, progress and errors.
  * Never translated: what the interviewer says and its captions, follow-ups, STT transcripts, question banks, JD and CV content, AI evaluation feedback, and backend error text. The UI maps error codes to its own messages instead.
  * No locale is sent to interview, AI or other backend calls. CI guards catalog parity, unused keys and new hard-coded strings.
  * The epic's original wording kept the whole room in English. The implemented room chrome is translated, and this still needs the product owner's confirmation.

---

## 20. Leave Ends the Interview; No Pause (Settled 2026-11-03, KAN-215/174/180/272, PR #95)
* **Decision:** There is no pause. The room's Leave confirms, then ends and submits the session. A session idle for 15 minutes is ended by the System Handler.
* **Rationale:** A pause adds states, recruitment deadline edge cases and abuse paths for little value in a practice or screening interview.
* **Rule:**
  * One idempotent end path serves a normal completion, a Leave (`POST /interviews/{id}/complete`) and an abandonment. It sets `completed_at` and `end_reason` (`completed` / `left` / `abandoned`) once. Evaluation runs on what was answered; unanswered core questions stay unanswered.
  * Practice: no refund and no second charge. Recruitment: the Application moves on as after a normal completion, and the slot was consumed at start.
  * An accidental disconnect is not a leave; reconnecting resumes the same session.
  * Abandoned means no delivered question and no stored answer for 15 minutes, derived from existing timestamps.
  * Recording upkeep jobs: see Decision 18. All jobs run in-process with a PostgreSQL advisory lock per job.

---

## 21. AI Task Routing (Implemented 2026-11-03, KAN-273, PR #93)
* **Decision:** Each AI task can be routed to its own provider with an ordered fallback, to take load off the single Gemini key.
* **Rule:**
  * Opt-in per task through configuration; unset tasks keep the existing Gemini behavior.
  * Product-owner target: Groq GPT-OSS-120B for extraction, bank and evaluation; GPT-OSS-20B for the live follow-up; Gemini as fallback; Groq vision (`qwen/qwen3.8-27b`) for scanned JDs later.
  * Bedrock (verification and fallback) and Jev (evaluation and follow-up decision, KAN-274) are future adapters behind the same seams, not dependencies.
  * Provider responses must pass the existing validators; prompts, answers and keys are never logged.

---

## 22. Interviewer Performance: Cues and Motion (Implemented 2026-11-03, KAN-250/251, PR #96)
* **Decision:** The server decides how each line is performed with a bounded vocabulary; the client plays it back with a small motion set.
* **Rule:**
  * Emotions `neutral`, `friendly`, `curious`, `encouraging`, `thinking`; gestures `nod`, `explain_hand`, `lean_in`, `greet`, `listen`. Nothing else is ever sent.
  * Core questions use rules (no model call). Follow-ups use up to 4 cues proposed by the existing follow-up decider, validated, else `curious` + `lean_in`. Cues are anchored to words and frozen with the turn, so a reconnect replays them.
  * The motion set is Mixamo-sourced, converted to VRMA. Its use is accepted for this non-commercial capstone, with sources recorded in `frontend/public/motions/CREDITS.md`. Lip-sync owns the mouth; the interviewer also reacts while the Candidate speaks.

---

## 23. Photographic 3D Interview Rooms (Implemented 2026-11-03, KAN-275, PR #94)
* **Decision:** The room shows realistic scenes (café, office, meeting room) instead of flat images.
* **Rule:**
  * Each scene is a Poly Haven CC0 photographic HDRI on a grounded skybox with image-based lighting, plus one 3D table in front of the upper-body interviewer.
  * Scenes are lazy-loaded and size-budgeted. A failure or weak WebGL falls back to the scene's still image.
  * Full-GLB rooms (for example Sketchfab) were not used because their download API needs a token; the meeting room reads as a backdrop rather than a full conference room.


---

## 24. Reusable Rubrics, Question Expected Answers and Metric-Based Overall (Agreed 2026-11-07; Foundation Implemented 2026-11-11, KAN-325/326/332, PR #127; Generation, Readiness and Locks Built, Pending Merge; Live Grading, Results and Reports Pending)

* **Decision:** Keep metrics and posting weights. Add reusable rubrics by question type, criteria linked to metrics, and question-specific expected_answer. AI proposes criterion points/evidence; Go calculates question metric scores, session metric scores and weighted overall.
* **Reason:** Generic metric names leave too much grading discretion to AI. Shared scoring structures reduce recruiter setup across a large bank, while a private expected answer grounds each question in its own content. Rubrics improve explainability; they do not certify AI correctness or authorize automatic hiring.
* **Configuration:** Two new tables, rubrics and rubric_criteria, plus core_questions.rubric_id and expected_answer TEXT. A crit belongs to one rubric and exactly one metric. Expected points do not get a separate table. Bloom is optional cognitive-demand metadata, not points or a metric.
* **Authoring:** Admin manages system templates. Recruiter creates private rubrics from scratch or copies permitted system/own templates, edits eligible own data and assigns to own questions. No editing/reading other recruiters' private templates. This explicitly supersedes the prior system-rubric-template exclusion; raw-prompt editing, historical score editing and overriding private posting weights remain excluded.
* **Generation/review:** Generate proposes allowed rubric_id and expected_answer with each question. Recruiter reviews groups and exceptions; no manual reconstruction of 50 identical scoring structures. Bank size (default 20 since 2026-11-11, previously 50) differs from the sampled session core count (default 8).
* **Calculation:** Sum/normalize crits by metric within each core question, average each metric over the questions that assess it, then apply custom posting weights or equal defaults (Decision 13a). Whole-question scores are diagnostic, not averaged into overall. Follow-ups contribute to their parent core, not extra weight.
* **Storage meaning:** score_details remains one score per session/metric; interviews.score remains the session overall. Persisting actual criterion results requires result storage beyond the two configuration tables; the proposed interview_criterion_scores and interview_questions.score are engineering details in the spec.
* **No snapshots:** No rubric snapshot/version tables are introduced. Used rubric/criterion definitions, question content/expected answers/bindings are locked when approved or referenced by a started session; copy to change for eligible drafts. Hard-delete only unreferenced data; archive stops new selection and retains existing references. History is not regraded.
* **Disclosure:** Candidate recruitment reports are status-only, including history/export/derived signals; scores, expected answers, grading evidence, transcripts and recordings stay hidden. This reconciles the earlier score-visible Brain text with the later explicit product decision and current code. Owning recruiter review remains in Application Detail and retains its final-decision deadline gate.
* **Development:** Disposable dev data may be reset during implementation; no data-backfill or legacy-score compatibility system is required. Spec publication itself is not a database reset.
* **Settled 2026-11-11 during implementation:**
  * *Scoring policy (RB-08):* overall and metric scores keep 2 decimals; a missing metric coverage withholds the overall instead of counting as 0; a partial session is still scored when every active metric has evidence. Recruitment pass/fail thresholds remain unset.
  * *Management limits (RB-02):* 1 to 10 criteria per rubric; `max_points` is an integer 1 to 10; name at most 120 characters, description 1000, criterion name 160, scoring guide 2000. Concurrent edits use `updated_at` as the token (409 `RUBRIC_STALE`); no revision column. Other stable codes: `RUBRIC_NOT_FOUND` (404), `RUBRIC_LOCKED`, `RUBRIC_IN_USE` (409). Migration 000032 seeds 4 system templates with 14 criteria of 10 points each.
  * *Generation (RB-05):* one generation call returns each question with an allowed `rubric_id` and an `expected_answer`; no separate classification call. The allowed set is the active system rubrics plus the posting owner's active rubrics, bounded (at most 20 rubrics, 8 criteria each) and passed as sanitized untrusted data. `expected_answer` is at most about 80 words (the server accepts up to 100 words and 1000 characters). An id outside the allowed set rejects the whole batch for retry; a rubric that is archived or no longer permitted at save time stores the question without a rubric; an answer that looks like prompt injection is stored as null. Questions lacking a valid rubric or answer are marked grading-incomplete and are never auto-confirmed. Recruiter DTOs carry the grading fields; runtime and Candidate DTOs never do.
  * *Readiness and withdrawal (RB-07):* submitting or resubmitting a posting whose bank has grading-incomplete questions is refused with 409 `GRADING_CONFIG_INCOMPLETE` (a rubric archived after assignment counts as incomplete); Admin approval re-checks under the same posting lock. Adding, editing the text of, or deleting a question clears `review_submitted`; saving identical text does not.
  * *Lock rule:* a rubric and its criteria are locked as soon as any question references it, not only after approval or a started session. Locked data is not edited or hard-deleted (even by Admin for system templates); archive stays allowed and keeps references; copy is the way to change. A relaxed rule (lock only when approved or used) was rejected because it needs a submission-time column and creates a lock-ordering deadlock risk between bank edits and rubric edits.
  * *Question bank lock:* once the posting is approved the bank, including each question's rubric and expected answer, is locked (Decision 13).
* **Implementation review items still open:** result-table DDL and numeric storage, metric-coverage sampling, practice activation, calibration thresholds and owner. The 2-decimal and withhold rules above settle only the calculator-level behavior.
* **Source:** [[01_Domains/Evaluation/Scoring-Rubrics-Spec|Detailed Vietnamese spec with the full 2/2 → metric → overall example]]. The new management capabilities require formal SRS/UC/ERD reconciliation; existing frozen IDs/count are not automatically renumbered here.

## 25. Per-Role-Profile Learning Roadmap (Agreed 2026-11-07; Implementation Pending)

* **Decision:** Each Candidate Role Profile owns one private Roadmap. Create its row with the profile; generate ordered knowledge topics only after the Candidate confirms requirements. FE renders topics linearly by position, without sequential unlock gates.
* **Evidence and math:** Only completed, successfully evaluated practice contributes. Take the latest result for each distinct core question in a topic, then the five most recent distinct questions; average their normalized Barem question scores. Fewer than five is provisional; five with average >=80 is achieved. Roadmap progress is achieved-topic count / total-topic count; all nonempty ready topics must be achieved for completed. New lower results can return achieved/completed to in_progress. These product thresholds are not a validated Senior certification.
* **Boundary:** Topic is subject matter, metric is an assessment dimension, rubric is reusable grading guidance. Roadmap uses question totals; it does not change Decision24's metric-weighted interview overall. Recruitment scores/derived signals never feed Roadmap.
* **Data:** Add roadmaps, roadmap_items and roadmap_assessments, plus core_questions.roadmap_item_id and interviews.focus_item_id. generation_status is persisted separately from derived learning_status. No archived_at, manual activity progress, topic editor, snapshot/version tables or independent score cache.
* **Practice:** General or one-topic focus on the same profile; default 8 core questions,5 coins on successful start, resume free. Prioritize unseen questions/unachieved topics within Barem metric coverage. A bank of 50 overall is not proof of coverage for each topic; validate/top up before start, never substitute another topic or charge on generation failure.
* **Result validity:** Explicitly skipped/delivered unanswered core questions receive 0 when the session is finalized. Unasked questions and technical grading/media errors are not zero evidence. Only finalized successful evaluation updates progress; follow-ups remain part of their core, and retries are idempotent.
* **Structure:** No direct user editing of topics/scores/status/thresholds or AI topic changes after interviews. Regenerate only before any practice session, replacing topic/bank mapping consistently under a start/regenerate guard. After first start, keep structure; a changed goal uses a new profile/Roadmap. Privacy erasure remains supported.
* **Reliability:** Assessment links reference existing question results. Use event time, not catch-up insert time, for recency. Existing System Handler reconciliation must recover missed updates after restart; Roadmap update failure does not discard a saved interview result.
* **Source:** [[01_Domains/Learning-Roadmap/Learning-Roadmaps-Spec|Detailed Vietnamese spec and worked examples]]. The formal SRS/UC/ERD must be reconciled during implementation; frozen IDs/count are not silently renumbered. This decision records agreed future behavior, not applied migrations or completed implementation.

## 26. Minimal Moderated Avatar Marketplace (Agreed 2026-11-07; Implementation Pending)

* **Decision:** Candidates and Recruiters may list their own distributable avatar for Admin review, browse approved listings and buy a non-exclusive copy into their own inventory. Seller retains the original. Admin moderates but has no inventory/wallet and does not author an asset catalog. Supersedes Decision 10 and extends Decisions 6/7/8/15 only as stated here.
* **Money:** Use coins, seller receives the full price, no Marketplace fee/cash-out in MVP. Debit buyer, credit seller, record one COIN transaction and grant one buyer-owned avatar in the same DB transaction. Same-intent retry must not charge/grant twice; no PayOS call except separate existing coin top-up.
* **Data:** Exactly two new tables: avatar_assets and avatar_listings. avatars gains asset_id and moves model_path into assets. No preview_path/source fields, avatar_purchases/entitlement table, purchase_transaction_id or Marketplace columns on transactions. Purchase details are human-readable description in existing transactions; never parse it into a fake FK/rights registry.
* **Ownership:** Buyer uses their own avatar row through existing inventory/setup permissions, sharing an immutable asset with seller/other buyers. Do not select the seller's private avatar ID. A bought copy occupies one slot; no duplicate purchase while that asset is already in the inventory. Delete frees the row/slot and does not retain a free restore entitlement; a new purchase after deletion is a new intent/key.
* **Lifecycle:** Draft/rejected listings can be edited/submitted; Admin approves/rejects. Seller may delist and Admin may suspend to prevent new purchases. Neither operation removes avatar copies already granted. Asset/seller identity is fixed on a listing; normal business deletion does not erase financial history. Source-avatar deletion/account erasure must not remove shared files still used by buyers.
* **Use cases:** Seven Candidate/Recruiter capabilities (browse/search, detail, My Listings, create/submit, edit/resubmit, delist, purchase) and three Admin capabilities (queue, review, suspend). Existing inventory/setup/history extend instead of adding duplicate modules. Do not silently renumber frozen formal UC IDs/count.
* **Limits:** No per-listing sales dashboard, exact transaction-to-product FK, free restore after delete, per-buyer revoke/refund or structured purchase history. Transactions remain financial history; use inventory ownership to allow normal avatar use. Provider redistribution permission must be established for assets before live sales; product scope approval is not vendor authorization.
* **Source:** [[01_Domains/Avatar-Marketplace/Avatar-Marketplace-Spec|Approved Vietnamese spec]]. These are agreed future capabilities; target schema/API/index/retry contract still require engineering review. Task plan must be approved before Jira creation.

---

## 27. Interview Behavior Guidelines (Implemented 2026-11-11, KAN-276, PR #128)

* **Decision:** An Admin configures a small, closed set of guidelines that shape how the AI interviewer's follow-up questions are decided and worded. This settles the fields, persistence, versioning and prompt composition that Decision 16.6 left open.
* **Scope of effect:** Core questions come from the bank and are not LLM-written at interview time, so the only LLM-driven interviewer behavior is the follow-up decider. Guidelines change how follow-ups are decided and worded and how many are asked. They never change core questions, scoring, reports or anything already stored.
* **Fields (every field is an enum, a bounded integer or a subset of a closed catalog; no Admin-typed string ever reaches a prompt):**
  * `tone`: `neutral` (default), `friendly`, `formal`, `challenging`.
  * `question_length`: `standard` (follow-up at most 300 characters, default) or `concise` (at most 150).
  * `follow_up_max`: unset (default) or 0 to 3. Effective limit is the smaller of the configured `interview.follow_up_limit` and this value; Admin cannot raise the ceiling, and 0 means no follow-ups.
  * `follow_up_focus`: `standard` (default), `vague_only`, `thorough`.
  * `avoid_topics`: up to 5 distinct values from a fixed catalog (compensation, personal life, politics and religion, former employers, location and nationality).
  * Not fields: interview language (the interview is always English), raw prompt text, scoring weights or rubrics, pricing, provider or model selection, feature toggles.
* **Persistence and versioning:** Append-only versions in `interview_behavior_guidelines` (the current guideline is the highest version; none saved means defaults). Each save records who and when. Admin reads and writes through `GET/PUT /admin/interview-guidelines`; the PUT carries `expected_version`, and a stale value is refused with 409 `GUIDELINE_VERSION_CONFLICT` so two Admins cannot silently overwrite each other. Candidates and Recruiters never see the values.
* **When changes apply:** Only to sessions that start after the save. At session start the current version is recorded in `interviews.behavior_guideline_id` and that session uses exactly that snapshot for every follow-up, including after a reconnect or resume. Running sessions and completed interviews are unchanged. With nothing saved, a NULL snapshot or all-default values, the follow-up instruction is byte-for-byte the pre-guideline text.
* **Prompt composition:** Each value maps to a fixed sentence in code, inserted as one optional block between the "ask a follow-up only when" paragraph and the output-schema paragraph, ending with a sentence saying the earlier rules (untrusted data, output format, one short question) always win. Unknown stored values count as defaults and are never interpolated. The guideline is never placed in the user message or any `untrusted_*` field. The follow-up validator (English, one line, one question mark, no repeats) still runs on every reply, and `concise` lowers its length cap too.
* **Honest limit:** `tone`, `follow_up_focus` and `avoid_topics` steer the model through the prompt and cannot be proven to change every reply; what is guaranteed is that the saved value reaches the prompt exactly as specified and the guards still hold. `follow_up_max` and `question_length` are also enforced by code.
* **Still excluded (Decision 16.6):** unrestricted raw system-prompt editing, overriding posting weights or historical results, runtime toggles, LLM provider configuration, coin pricing. The frozen Use Case gap and the 62-count convention are unchanged.
