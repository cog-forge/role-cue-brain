---
title: Core Flows
tags:
  - flows
  - workflows
  - practice-flow
  - application-flow
  - avatar-flow
aliases:
  - Core Flows
  - User Journeys
---

# Core Product Flows

This document details the primary end-to-end user workflows within RoleCue:
1. **Flow A:** Candidate Practice Flow (The flagship technical interview simulation)
2. **Flow B:** Job Application Flow (Lightweight recruiter job posting & candidate application)
3. **Flow C:** Personal Avatar Generation Flow (Photo-to-3D personal avatar creation)

---

## Flow A: Candidate Practice Flow

The Candidate Practice Flow is the central value engine of RoleCue. It takes a raw target job description, guides the candidate through structured requirement review and refinement, enforces human confirmation of requirements before question-bank generation, configures presentation, debits the practice fee in coins at session start, executes real-time 3D simulation with adaptive questioning, and delivers diagnostic evaluation. The system prepares its single hidden core-question bank Blueprint and immutable execution context internally.

```mermaid
flowchart TD
    A["Raw Target JD<br/>(Text input or PDF upload)"] --> B["AI Competency Extraction<br/>(Languages, Frameworks, DBs, Seniority)"]
    B --> C["Candidate Reviews Extracted JD<br/>(Inspect titles, tags, and categories)"]
    C --> D["Candidate Refinement Notes<br/>(e.g., 'Exclude C#', 'Focus on Kafka')"]
    D --> E["Candidate Confirms Requirements"]
    E --> F["Autonomous Question-Bank Blueprint Generation<br/>(1 Current Core-Question Bank; Strictly Hidden)"]
    F --> G["Configure Interview Session<br/>(Interviewer, Voice, Environment, Difficulty, Time)"]
    G --> H["Readiness & Session Start<br/>(Audio test; Practice fee debited from Coin Wallet)"]
    H --> I["Live 3D Virtual Interview Simulation<br/>(Random x core questions + bounded follow-ups)"]
    I --> J["Multi-Dimensional Evaluation Report<br/>(Scored against assigned questions; Radar chart & Roadmap)"]
```

### Step-by-Step Breakdown

#### 1. Ingestion & Preprocessing
* The Candidate provides a target Job Description by either pasting raw text or uploading a PDF document.
* If a PDF is uploaded, text is extracted in natural reading order and character encodings are normalized into clean UTF-8.

#### 2. AI Competency Extraction & Deterministic Validation
* The normalized text is parsed by an LLM via structured extraction prompts.
* The system extracts:
  * Role Title and Seniority Level (`intern`, `junior`, `mid`, `senior`, `lead`).
  * Technical Skills categorized by: `programming_language`, `framework`, `database`, `tool`, `technology`, `other`.
  * Requirement weights (`required` vs. `preferred`).
  * Technologies and Core Domain Knowledge.
* The extracted payload is deterministically validated before presentation. Duplicate or conflicting skills are reconciled. Soft skills are intentionally excluded.

#### 3. Candidate Review, Natural-Language Refinement, & Confirmation
* The Candidate reviews the extracted requirements in the user interface.
* The Candidate may:
  * Adjust the job title or seniority badge.
  * Add missing technologies or remove irrelevant tags.
  * Enter **Natural-Language Refinement Notes** in a dedicated instruction field (e.g., *"Exclude C# from the interview"*, *"Focus heavily on system architecture and distributed caching"*).
* **Human Confirmation Precedes Blueprint Generation:** The Candidate explicitly confirms the reviewed requirements. Confirmation is the gate authorizing question-bank generation.

#### 4. Autonomous Question-Bank Blueprint Generation (INTERNAL & HIDDEN)
* Following requirements confirmation, the system generates a single **Interview Blueprint** serving as the core-question bank:
  $$\text{Interview Blueprint} = \text{Confirmed Requirements} + \text{Candidate Refinement Notes}$$
* The blueprint is ONLY the persistent/current core-question bank/list generated from confirmed skills, requirements, and seniority/refinement context. It contains exclusively the bank of core questions. It does NOT contain grading rubrics, evaluation criteria, competency weights, depth benchmarks, or evaluation matrices; evaluation configuration is a separate concern.
* Exactly **one current Blueprint** exists per JD (it is absent until generated; changing configuration or difficulty does not spawn multiple current banks).
* > [!IMPORTANT]
  > **The Blueprint is strictly INTERNAL and HIDDEN from Candidates.** Candidates **never view, edit, or confirm the Blueprint**. They interact exclusively through the live simulation.

#### 5. Configure Interview Session
* The Candidate configures presentation and execution parameters:
  * **Interviewer Persona:** Visual appearance choice (system preset or eligible model from the Candidate's **own** personal avatar inventory; another user's avatar is never usable).
  * **Voice Profile:** Voice profile choice (tone, accent, gender) sourced from TTS providers; only an active Voice Profile can be selected.
  * **Interview Environment:** 3D virtual room background choice.
  * **Difficulty & Horizon:** Session difficulty and duration horizon.

#### 6. Readiness Verification & Start Charging
* The Candidate completes **Test Audio and Interview Readiness** (microphone and audio check).
* **Start Charging Boundary:** When the Candidate initiates the practice session, the practice interview fee (**5 coins**) is debited from their **personal coin wallet**.
* If a session experiences a network disconnect or is paused, reconnecting or resuming the same session incurs **no second charge**.
* Starting again while an unfinished practice session exists for the same Role Profile returns that session instead of charging again; a retried start with the same idempotency key returns the session it created.

#### 7. Live Virtual Interview Simulation
* The session assigns its selected questions via `interview_questions` (linking each turn position to its persistent `core_questions` record) and applies the evaluation configuration actually used (without modeling evaluation criteria as part of the current Blueprint).
* The simulation selects a random set of $x$ core questions from the bank and may ask bounded follow-ups based on the candidate's answers and interview context.
* Execution loop:
  1. The interviewer articulates the question via TTS with synchronized blend-shape visemes.
  2. The Candidate speaks their answer; Voice Activity Detection (VAD) monitors speech boundaries and streams audio to STT.
  3. STT produces the transcript turn.
  4. The runtime analyzes the Answer with Interview Context to determine whether to ask a bounded follow-up or proceed to the next core question. Deciding whether to follow up and generating follow-up wording are decoupled responsibilities without premature vendor lock-in.
  5. The loop repeats until all selected questions are completed or the horizon ends.
* If client hardware cannot sustain WebGL 3D rendering, the interface gracefully degrades to a 2D animated waveform display without dropping voice dialogue.

#### 8. Evaluation & Learning Roadmap
* Upon session completion, the turn transcript is graded against the assigned questions and evaluation configuration across technical competencies.
* Evaluation results are stored relationally across `interviews.score`, `interviews.feedback`, `score_details (interview_id, metric_id, score)`, and `conversation_turns.feedback`, and displayed on the candidate's dashboard featuring an overall score (0–100), competency breakdown, radar chart, turn-by-turn critiques with model answers, and a prioritized study roadmap.

---

## Flow B: Job Posting & CV-First Application Flow

RoleCue incorporates a lightweight job board and CV-first recruitment flow connecting Candidates and Recruiters. The scope is strictly bounded to eliminate full ATS overhead.

> [!IMPORTANT]
> The sequence below is the ratified business contract. Moderation approval and the Recruiter's Open Intake action are conceptual steps whose persistence mapping remains unresolved: the frozen `job_posting_status` enum has only `pending`, `open`, `intake_closed`, `closed`, and `rejected`, with no `approved` value or `intake_status` column. **Known implementation gap:** the current interim implementation sets `open` at Admin approval, so Candidates may apply before funding; CV approval and recruitment interview start still require funded capacity. This does not change the canonical sequence or replace the funding gate. See [[02_System/Domain-Model|Domain Model]] and [[03_Decisions/Product-Decisions|Product Decisions]] (Decisions 13 and 16).

```mermaid
sequenceDiagram
    autonumber
    actor Recruiter
    actor Candidate
    actor Admin
    participant System as RoleCue Platform
    participant DB as Platform Storage

    Note over Recruiter,Admin: 1. Job Posting Setup & Approval
    Recruiter->>System: Create Job Posting from JD-like content
    System->>System: AI extracts technical requirements
    Recruiter->>System: Review & confirm requirements
    System->>DB: Persist Job Posting (status = pending, in preparation)
    Recruiter->>System: Generate single Question-Bank Blueprint (once)
    Recruiter->>System: Edit own-posting core questions & review evaluation settings (custom or defaults)
    Recruiter->>System: Select company 3D interviewer model and Voice Profile
    Recruiter->>System: Submit Job Posting for review (non-empty bank required)
    System->>DB: Mark Posting submitted (enters Admin queue)
    Admin->>System: Approve Job Posting
    System-->>Recruiter: Moderation approved (Conceptual, funding required before intake)

    Note over Recruiter,System: 2. Slot Funding & Intake Open
    Recruiter->>System: Fund interview capacity using Coins from Wallet (interview_slot)
    System->>DB: Atomically debit wallet, record transactions entry & update job_postings.interview_slot (Idempotent)
    break Funding fails
        System-->>Recruiter: No partial debit or capacity, intake remains unavailable
    end
    System-->>Recruiter: Funding committed (Duplicate requests do not charge or allocate twice)
    Recruiter->>System: Open application intake after successful funding
    System->>System: Require moderation approval, committed funding & Recruiter Open Intake action
    Note over System,DB: Approval / funding / intake persistence mapping unresolved, interim code sets open at approval

    Note over Candidate,Recruiter: 3. Unlimited Application Intake & Immediate Visibility
    Candidate->>System: Browse moderated, funded Postings with Recruiter-opened intake
    Candidate->>System: Submit Application with uploaded CV (Unlimited submissions allowed)
    System->>DB: Persist Application with CV (Status: PENDING)
    System-->>Recruiter: Application & CV immediately visible in View Application Detail

    Note over Recruiter,Candidate: 4. CV Screening & 24h Deadline
    Recruiter->>System: Screen CV in View Application Detail (Max interview_slot approvals)
    alt Recruiter rejects CV
        Recruiter->>System: Reject CV
        System->>DB: Update Application (Status: REJECTED - Terminal)
        System-->>Candidate: Notify CV rejected
    else Recruiter approves CV
        Recruiter->>System: Approve CV (Grants eligibility, does not consume slot yet)
        System->>DB: Persist interview_deadline = approval_time + 24h
        System-->>Candidate: Notify interview eligible with 24h deadline
    end

    Note over Recruiter,System: 5. Close Intake (Manual or When Limitation Met)
    opt Close Intake triggered (Manual or configured limit met)
        System->>DB: Set Job Posting status = intake_closed (Stop new applications)
        System->>DB: Auto-reject remaining unscreened/not-approved Applications
        Note over System,DB: Approved candidates remain interview-eligible. Close intake itself refunds nothing. No reopen flow.
        Note over System,DB: No Application still in progress? Terminal close and refund follow in the same operation
    end

    Note over Candidate,System: 6. Recruitment Technical Interview & Slot Consumption
    opt Approved Candidate completes interview before deadline
        Candidate->>System: Start recruitment interview
        System->>DB: Create the Application's single recruitment session, consuming 1 funded slot (Free reconnect)
        Note over Candidate,System: Uses locked company 3D model & Voice Profile
        System->>DB: Record recruitment session, audio/video recordings & transcript
        System->>DB: Evaluate session and store Interview Result
        System->>DB: Attach Interview Result & recordings to Application
        System-->>Candidate: Display overall recruitment score (Transcripts/recordings hidden)
    end
    opt Candidate fails to complete interview before interview_deadline
        System->>DB: Deadline expired -> Auto-reject Application (Status: REJECTED - Terminal)
    end

    Note over Recruiter,System: 7. Hard-Gated Final Review & Terminal Auto-Close
    Note over Recruiter,System: Final decision HARD-GATED until MAX(interview_deadline)
    opt After MAX(interview_deadline)
        Recruiter->>System: Open View Application Detail (Reviews CV, scores, recordings)
        alt Recruiter Approves
            Recruiter->>System: Approve Application (Status: APPROVED - Terminal)
        else Recruiter Rejects
            Recruiter->>System: Reject Application (Status: REJECTED - Terminal)
        end
    end

    opt Intake closed and every Application terminal (APPROVED / REJECTED), or no Applications
        System->>DB: Terminal Job Posting Auto-Close
        System->>DB: Refund interview_slot minus consumed sessions as internal Coins to Recruiter Wallet (once)
        Note over System,DB: Refund unused JP Candidate Slot (Internal coin movement, no PayOS call)
    end
```

### Step-by-Step Breakdown

1. **Job Posting Creation & Question Bank Setup:**
   * A Recruiter creates a **Job Posting** (the company's Job Description) from JD-like content.
   * AI extracts structured requirements, and the Recruiter reviews and confirms them.
   * The Recruiter generates a single **Interview Blueprint** (core-question bank, persisted in `core_questions`) for the posting, once; a failed generation may be retried.
   * The Recruiter can view and edit core questions within their own posting's question bank while the Posting is `pending` or `rejected`; the bank is locked from `open` onward.
   * The Recruiter prepares valid posting-level evaluation settings (separate from the question bank): optional custom metric percentages total 100%; otherwise use effective equal-split defaults (currently five metrics at 20% each). Custom weights are not required for Admin approval.
   * Before submission for Admin approval, the Recruiter locks the company 3D interviewer model and Voice Profile.
   * Generating the bank does not submit the Posting. The Recruiter explicitly submits for moderation, which requires a non-empty bank; Admin acts only on submitted Postings, and deleting the last question withdraws the submission. A rejected Posting may edit its bank and resubmit.
   * Administrator approval is a moderation decision and, in the canonical flow, does not itself open intake or make the Posting discoverable (the current interim implementation sets `open` at approval; known implementation gap, Decision 13).
2. **Interview Slot Funding & Intake Open:**
   * After moderation approval, the Recruiter funds interview capacity using personal wallet coins, exactly once per Posting at 1 coin per slot (no top-ups; repeating the same count charges nothing, a different count is refused). The coin debit, unified `transactions` ledger entry, and `job_postings.interview_slot` update must commit atomically and idempotently; failures or duplicate requests must not leave inconsistent capacity or double charges. PayOS is only for real-money coin-package top-ups.
   * **`interview_slot` Definition:** Represents exclusively maximum recruitment interview capacity (the maximum number of Candidates that may be approved to proceed into the recruitment interview stage). It does **not** mean total Applications/CVs submitted, company hiring headcount, or final hires. RoleCue does not persist or enforce company hiring headcount.
   * Only after successful funding does the Recruiter open application intake. Candidates may discover and apply only when moderation approval, funding, and this intake-opening action are all satisfied; the persistence mapping remains unresolved, and the interim implementation's approval-to-`open` behavior is a known gap.
3. **Application Intake & Immediate Recruiter Visibility:**
   * While Job Posting intake is **OPEN**, Candidates may submit an **UNLIMITED** number of Applications with uploaded CV/resumes.
   * Application submissions are **NOT limited by interview capacity** (`interview_slot`).
   * **Immediate Visibility:** The Application and uploaded CV/resume are immediately visible to the owning Recruiter in **View Application Detail** upon submission, prior to any interview taking place.
4. **Recruiter CV Screening & 24-Hour Interview Deadline:**
   * The Recruiter screens Applications and uploaded CVs inside **View Application Detail**.
   * The Recruiter may approve at most `interview_slot` Candidates to proceed to the recruitment interview stage.
   * **CV Approval Semantics:** Approving a CV grants interview eligibility; it does **not** consume the funded interview slot yet.
   * **Interview Deadline:** When an Application is approved:
     $$\text{interview\_deadline} = \text{approval\_time} + 24\text{ hours}$$
     (The application service uses the approval instant as `approval_time` to calculate and persist `applications.interview_deadline`; `approval_time` is conceptual and not a persisted column).
   * A Candidate must COMPLETE the recruitment interview before `interview_deadline`. Merely starting the interview before the deadline is insufficient; once the deadline passes, further answers and completion are refused. If an approved Candidate does not complete the recruitment interview by `interview_deadline`, the Application automatically transitions to terminal `REJECTED`.
5. **Close Intake (Manual or Limitation-Driven):**
   * Intake can be closed manually by the Recruiter (*Close Job Posting Intake*) or automatically by the System Handler (*Close Job Posting When Meet Configured Limitation* upon reaching `interview_slot` approved candidates).
   * **Close Intake Semantics:**
     * Stops accepting new Applications/CVs.
     * Automatically **REJECTS** all remaining Applications that are still unscreened or not approved to proceed to interview.
     * Candidates already approved for interview remain interview-eligible and may still complete their interviews within their 24-hour windows.
     * Close Intake itself is **NOT terminal Job Posting close** and does **NOT refund unused interview capacity**.
     * The terminal close rule is evaluated in the same operation: when no Application is still in progress (including a Posting with zero Applications), the Posting goes straight to terminal close, and the refund comes from that step.
     * Closing intake on an `intake_closed` or `closed` Posting again succeeds without change.
     * There is **NO Reopen Intake flow** and **NO manual End Recruitment command**.
6. **Recruitment Interview Simulation & Slot Consumption:**
   * An applicant with approved interview eligibility launches the technical interview.
   * **Slot Consumption Boundary:** The funded interview slot is consumed strictly on the Candidate's **FIRST successful recruitment interview start**, when the Application's single recruitment session is created. Reconnecting to or resuming that active session consumes no additional slot.
   * The recruitment session has no Role Profile; it belongs to exactly one Job Posting through its Application and is presented under the Posting's title.
   * The Candidate is **never charged** for a Recruiter-funded recruitment interview.
   * The interview strictly runs using the Job Posting's locked company 3D interviewer model and Voice Profile, capturing audio/video recordings and turn transcripts.
7. **Result Attachment & Role-Specific Visibility:**
   * The evaluation engine generates the Interview Result (Performance Report) and attaches it, along with the audio/video recordings, to the candidate's Application.
   * **Candidate Visibility:** The Candidate can view their overall recruitment evaluation score, but **cannot** access recruitment transcripts or audio/video recordings during recruitment.
   * **Recruiter Visibility:** The owning Recruiter has full access to the applicant's profile, CV, evaluation breakdown, turn critiques, and full audio/video recordings and transcripts inside **View Application Detail**. A recording is available once it is processed (`record_status = ready`).
8. **Hard-Gated Final Recruiter Review & Terminal Decision:**
   * Recruiter final Approve/Reject decisions are **HARD-GATED** until:
     $$\text{MAX}(\text{interview\_deadline})$$
     across all interview-eligible Applications for that Job Posting.
   * Before that timestamp, the Recruiter **cannot** perform final Approve/Reject.
   * After the gate expires, the Recruiter opens **View Application Detail**, inspects candidate profile, CV, evaluation score, and authorized recordings/transcripts, and renders the definitive final decision: **Approve** or **Reject** Application.
   * All Recruiter CV, report, and recording review capabilities are consolidated inside **View Application Detail** (no separate top-level review use cases).
9. **Terminal Job Posting Auto-Close & Unused Slot Coin Refund:**
   * Once intake is closed, a Job Posting automatically reaches terminal closed state when **EVERY Application in scope has a terminal result** (`APPROVED` or `REJECTED`), or immediately when it has no Applications.
   * Terminal rejections include applications rejected during Close Intake cleanup, interview deadline expiry, Recruiter CV rejection, and final Recruiter rejection.
   * Upon terminal auto-close, the system executes **Refund unused JP Candidate Slot**, refunding all still-unused recruitment interview capacity (`interview_slot` minus consumed sessions) to the Recruiter's personal coin wallet as internal coins, exactly once per Posting.
   * This refund is an internal database ledger credit; it does **not** call PayOS or external payment gateways.
   * Terminal Job Posting close is strictly automated; there is **no manual End Recruitment command**.

---

## Flow C: Personal Avatar Generation & Inventory Flow

RoleCue supports personal 3D avatar generation through an embedded free Avaturn iframe experience. Every Candidate and Recruiter starts with **3 personal avatar storage slots** (ratified 2026-10-20); Admin has no personal inventory. Avaturn owns capture, validation, customization, and final GLB generation; RoleCue converts and persists the final production VRM asset.

```mermaid
flowchart TD
    START["User opens Avatar Studio in RoleCue<br/>(Candidate or Recruiter)"] --> CAP_CHECK{"Read Actual Capacity from API<br/>(Initially 3 storage slots)"}
    CAP_CHECK -->|At Capacity| MANAGE["Manage Inventory:<br/>Delete existing avatar OR purchase slot with Coins"]
    MANAGE --> CAP_CHECK
    CAP_CHECK -->|Capacity Available| IFRAME["Embedded free Avaturn iframe experience"]
    
    IFRAME --> CAPTURE["Avaturn capture instructions and 3 required photos"]
    CAPTURE --> VALIDATE["Avaturn validation and retake workflow"]
    VALIDATE --> PREVIEW["Avaturn preview avatar and customization UI"]
    PREVIEW --> GLB["Avaturn final GLB generation"]
    
    GLB --> SLOT_CHECK{"Free storage slot available?"}
    SLOT_CHECK -->|Inventory full - INVENTORY_FULL| MANAGE
    SLOT_CHECK -->|Has free slot| CONVERT["RoleCue receives GLB and converts it to VRM"]
    CONVERT --> PERSIST["RoleCue persists usable VRM asset in User Avatar Inventory<br/>(No charge, uses one free slot)"]
    PERSIST --> READY["Avatar available in personal library for eligible interview selection"]
```

### Step-by-Step Breakdown

1. **Avatar Inventory Capacity Check:**
   * A Candidate or Recruiter accesses the Personal 3D Avatar Studio from RoleCue.
   * **Capacity Invariant:** Each Candidate and Recruiter starts with **3 storage slots**. Creating an avatar costs nothing while a slot is free; there is no separate generation fee (Decision 7, settled 2026-10-27). Additional slots cost 1 coin each (1–10 per purchase, debited atomically through the shared ledger) and are permanent capacity; the frontend reads actual capacity from the authoritative API because purchases can increase it.
   * If the user's avatar inventory is at capacity, creation is refused (`INVENTORY_FULL`); the user must either remove an existing avatar (freeing its slot) or purchase an additional avatar capacity slot using coins from their personal wallet before creating a new avatar.
2. **Avaturn Embedded Experience:**
   * RoleCue embeds the free Avaturn iframe.
   * Avaturn independently presents capture instructions, collects the three required photos, performs validation and retake handling, renders the interactive preview, and provides accessories and customization.
3. **Asset Handoff & Conversion:**
   * Avaturn generates the final GLB asset and returns it across the iframe bridge.
   * RoleCue receives the GLB and executes deterministic conversion to the standardized VRM format.
4. **Successful VRM Persistence (no charge; uses one free slot):**
   * RoleCue persists the VRM model and associates it with the owning user (`user_id`).
   * **No Charge:** Persisting the avatar occupies one free storage slot and debits no coins. Opening the creator, Avaturn capture, GLB generation, conversion, and persistence are all free while a slot is available; the only avatar charge is buying storage slots.
5. **Eligible Interview Usage:**
   * Once persisted, the avatar is stored in the user's personal library.
   * A Candidate can select an eligible personal avatar as the interviewer appearance for personal Target JD practice sessions.
   * For Job Posting interviews, company presentation settings remain locked by the Job Posting.
