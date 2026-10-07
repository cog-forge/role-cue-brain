---
title: Job Posting and Application Domain
tags:
  - domain
  - job-posting
  - job-application
  - recruiter
  - lightweight-ats
  - cv-screening
aliases:
  - Job Posting Domain
  - Application Domain
---

# Job Posting & Application Domain

The **Job Posting & Application Domain** provides a lightweight recruitment board and CV-first application workflow, enabling Recruiters to advertise employer openings, fund interview slots, screen incoming CVs, oversee technical interview results and recordings, and render final hiring decisions.

---

## 1. Purpose

Bridge the gap between technical interview preparation and career discovery. Recruiters have a direct channel to publish job vacancies, fund interview capacity, screen CVs before interviews take place, and review interview performance evidence (scores and recordings), while Candidates have a targeted channel to apply for technical roles and demonstrate technical proficiency.

---

## 2. Core Concepts

* **Job Posting (`job_postings`):**
  A formal job vacancy created by a Recruiter from JD-like content, serving as the company's Job Description.
  * **Canonical Equivalence:** `Job Posting` **is** the company's Job Description. There is no separate "Corporate JD" entity or company-tenant system.
  * **Question Bank & Settings:** Generates a single internal core-question bank Blueprint (persisted in `core_questions`). The owning Recruiter can view and edit core questions for their own posting. The Recruiter also configures posting-level evaluation weights separately from the question bank.
  * **Interview Presentation:** Company 3D interviewer model and Voice Profile selected by the Recruiter before Admin approval. Candidates cannot override either setting for a recruitment interview.
  * **Lifecycle Status:** `PENDING_APPROVAL`, `APPROVED`, `REJECTED`, `CLOSED`.
  * **Intake Control (`intake_status`):** `OPEN` or `CLOSED`.
  * **Removal of Archive:** `Archive Job Posting` is removed from RoleCue's product contract. It is not a supported capability or lifecycle state.
* **Interview Slot (`job_postings.interview_slot`):**
  The frozen ERD field defining maximum recruitment interview capacity:
  * Specifies the maximum number of Candidates that may be approved to proceed into the recruitment interview stage.
  * Funded by the Recruiter using coins from their personal wallet upon posting setup.
  * **Non-Headcount Invariant:** It does **NOT** mean total Applications/CVs submitted, company hiring headcount, or final hires. RoleCue does **NOT** persist or enforce company hiring headcount.
  * Passing CV screening does **not** consume a slot yet.
  * Starting the interview consumes one funded slot strictly on the Candidate's **first successful start**. Reconnecting to or resuming that active session does **not** consume another.
  * Unused interview capacity is refunded as coins back into the Recruiter's personal wallet upon **terminal Job Posting auto-close** (`Refund unused JP Candidate Slot`).
* **Application Intake (Open vs. Close):**
  * `Open intake`: Actively accepts new candidate applications and CV submissions. Candidates may submit an **UNLIMITED** number of Applications/CVs; submissions are **NOT limited by interview capacity**.
  * `Close intake`: Halts receiving new applications. Executed manually by the Recruiter (*Close Job Posting Intake*) or automatically by the System Handler (*Close Job Posting When Meet Configured Limitation* upon reaching `interview_slot` approved candidates).
  * **Close Intake Invariant:** Closing intake automatically **REJECTS** all remaining Applications that are still unscreened or not approved to proceed to interview. Candidates already approved for interview remain interview-eligible and may complete their interviews. Close intake does **not** refund interview capacity and is **not** terminal Job Posting close. There is **NO Reopen Intake flow** and **NO manual End Recruitment command**.
* **Application (`applications`):**
  A candidate submission to an approved Job Posting containing candidate profile information and an uploaded CV/resume.
  * **Immediate Recruiter Visibility:** The Application and CV are immediately visible to the owning Recruiter upon submission inside **View Application Detail**, prior to any interview taking place.
  * **24-Hour Interview Deadline:** When an Application is approved by the Recruiter, it is assigned:
    $$\text{interview\_deadline} = \text{cv\_approved\_at} + 24\text{ hours}$$
    If the candidate fails to complete the interview by this deadline, the Application automatically transitions to terminal `REJECTED`.
* **Two Distinct Decisions Consolidated in View Application Detail:**
  1. **CV Screening Decision:** The Recruiter's initial evaluation of the uploaded CV/resume within View Application Detail. Recruiter may approve at most `interview_slot` Candidates to proceed to interview. Approving grants interview eligibility and starts the 24-hour deadline; it does not consume a funded slot yet.
  2. **Hard-Gated Final Decision:** The definitive, terminal verdict rendered by the Recruiter (`Approve` or `Reject` Application) inside View Application Detail. This decision is **HARD-GATED until MAX(interview_deadline)** across all interview-eligible Applications for the posting. After the gate, the Recruiter reviews candidate profile, CV, and authorized Interview Result and recordings/transcripts to render the decision.
  * **Consolidated Review Invariant:** Recruiter viewing of CV, results, evaluations, and recordings is consolidated inside **View Application Detail**. Do not maintain separate current top-level review use cases.
* **Terminal Job Posting Close:**
  A Job Posting automatically reaches terminal closed state when **EVERY Application in scope reaches a terminal result** (`APPROVED` or `REJECTED`). Unused interview capacity is refunded to the Recruiter's wallet as internal coins upon terminal close. There is no manual End Recruitment command.

---

## 3. Actors Involved

* **Recruiter:**
  * Creates Job Postings from JD-like content; reviews and confirms AI-extracted structured requirements.
  * Views and edits core questions in the question bank (`core_questions`) for own Job Postings.
  * Configures posting-level evaluation weights across technical competencies.
  * Selects the company 3D interviewer model and Voice Profile before submitting for Admin approval.
  * Funds interview capacity (`interview_slot`) for the Job Posting using coins from personal wallet.
  * Manually triggers Close Job Posting Intake when desired (halts new applications; rejects unscreened/unapproved applications).
  * Inspects incoming Applications and CVs immediately upon submission inside View Application Detail.
  * Performs CV screening approval (max `interview_slot` approvals).
  * After the `MAX(interview_deadline)` hard gate, reviews applicant dossier, CV, technical evaluation scores, and recordings/transcripts in View Application Detail.
  * Renders definitive final decision: **Approve** or **Reject** Application.
* **Candidate:**
  * Browses and searches approved public Job Postings with open intake.
  * Submits an unlimited number of applications with uploaded CV/resumes while intake is open.
  * Tracks application status.
  * Upon receiving CV approval, participates in the required technical interview within the 24-hour deadline using the Job Posting's locked presentation and funded slot (Candidate is never charged).
  * Views overall recruitment evaluation score (transcripts and recordings remain hidden).
* **System Handler (Internal Automated Handler):**
  * Closes intake automatically when the configured interview capacity limit (`interview_slot`) is met (*Close Job Posting When Meet Configured Limitation*).
  * Automatically marks an Application terminal `REJECTED` if the candidate does not complete the interview before `interview_deadline`.
  * Automatically closes the Job Posting terminally and executes `Refund unused JP Candidate Slot` when every application has reached a terminal result (`APPROVED` or `REJECTED`).
* **Administrator:**
  * Views and filters submitted Job Postings.
  * Approves or rejects Job Postings for platform content and quality moderation.

---

## 4. Main Domain Flow

```mermaid
sequenceDiagram
    autonumber
    actor Recruiter
    actor Candidate
    actor Admin
    participant System as Job Posting & App Service
    participant Pay as Payment Domain
    participant DB as Relational Storage
    participant Mail as Email Provider

    Note over Recruiter,Admin: 1. Posting Setup, Core Questions & Approval
    Recruiter->>System: Create Job Posting from JD-like content
    System->>System: AI extracts technical requirements
    Recruiter->>System: Review & confirm requirements
    System->>DB: Generate single Question Bank (core_questions)
    Recruiter->>System: View/edit core questions & set evaluation weights
    Recruiter->>System: Select company 3D model and Voice Profile
    Recruiter->>System: Submit Job Posting for approval
    System->>DB: Store Job Posting (Status: PENDING_APPROVAL)
    Admin->>System: Approve Job Posting
    System->>DB: Update Job Posting Status to APPROVED

    Note over Recruiter,Pay: 2. Slot Funding & Intake Open
    Recruiter->>Pay: Fund interview capacity using Coins from Wallet (interview_slot)
    Pay->>DB: Record interview_slot on Job Posting
    Recruiter->>System: Open Application Intake (Intake: OPEN)
    System->>DB: Set intake_status = OPEN

    Note over Candidate,Recruiter: 3. Unlimited Application Intake & Immediate Visibility
    Candidate->>System: Browse Approved Postings (Intake: OPEN)
    Candidate->>System: Submit Application & upload CV (Unlimited submissions)
    System->>DB: Create Application with CV (Status: PENDING)
    System-->>Recruiter: Application & CV immediately visible in View Application Detail
    System->>Mail: Notify Recruiter of incoming application
    System-->>Candidate: Confirm application submitted

    Note over Recruiter,Candidate: 4. CV Screening & 24h Interview Deadline
    Recruiter->>System: Screen CV in View Application Detail (Max interview_slot approvals)
    alt Recruiter Rejects CV
        Recruiter->>System: Reject CV
        System->>DB: Update Application (Status: REJECTED - Terminal)
        System->>Mail: Send CV Rejection notice to Candidate
    else Recruiter Approves CV
        Recruiter->>System: Approve CV (Grants eligibility; no slot consumed yet)
        System->>DB: Set cv_approved_at & interview_deadline = cv_approved_at + 24h
        System->>Mail: Send Interview Eligibility notice with 24h deadline
    end

    Note over Recruiter,System: 5. Close Intake (Manual or When Limitation Met)
    opt Close Intake Triggered (Manual or Configured Limitation Met)
        System->>DB: Set intake_status = CLOSED (Stop new applications)
        System->>DB: Auto-reject remaining unscreened/not-approved Applications
        Note over System,DB: Approved candidates remain interview-eligible. No refund yet. No reopen flow.
    end

    Note over Candidate,System: 6. Recruitment Technical Interview & Slot Consumption
    opt Candidate interviews before deadline
        Candidate->>System: Start recruitment interview
        System->>DB: Consume 1 funded interview slot on FIRST successful start (Free reconnect)
        Note over Candidate,System: Runs locked company model & Voice Profile; captures video/audio & transcript
        System->>DB: Store Interview Result & recruitment recordings
        System->>DB: Attach Interview Result & recordings to Application
        System-->>Candidate: Display overall score (Recordings/transcripts hidden)
    end
    opt Candidate misses interview_deadline
        System->>DB: Deadline expired -> Auto-reject Application (Status: REJECTED - Terminal)
    end

    Note over Recruiter,Candidate: 7. Hard-Gated Final Review in View Application Detail
    Note over Recruiter,Candidate: Final decision HARD-GATED until MAX(interview_deadline)
    opt After MAX(interview_deadline)
        Recruiter->>System: Open View Application Detail (Inspect CV, scores, recordings)
        alt Recruiter Approves Application
            Recruiter->>System: Approve Application
            System->>DB: Update Application Status to APPROVED (Terminal)
            System->>Mail: Send Final Approval notice to Candidate
        else Recruiter Rejects Application
            Recruiter->>System: Reject Application
            System->>DB: Update Application Status to REJECTED (Terminal)
            System->>Mail: Send Final Rejection notice to Candidate
        end
    end

    Note over Recruiter,Pay: 8. Terminal Job Posting Auto-Close & Refund
    opt Every Application reaches terminal state (APPROVED / REJECTED)
        System->>DB: Set Job Posting Status to CLOSED (Terminal auto-close)
        System->>Pay: Trigger Refund unused JP Candidate Slot
        Pay->>DB: Credit still-unused interview_slot capacity as internal Coins to Recruiter Wallet
        Note over Pay,DB: Internal coin ledger credit; no PayOS call
    end
```

---

## 5. Business Rules & Invariants

1. **Non-ATS Boundary (Scope Termination):**
   * Recruitment scope strictly terminates at **Application Approve / Reject**.
   * RoleCue does **NOT** model or support:
     * Multi-stage recruitment pipelines (e.g., Phone Screen $\rightarrow$ Tech Interview $\rightarrow$ Culture Fit).
     * Interview panel scheduling or calendar synchronization.
     * Candidate ranking algorithms or automated resume scoring.
     * Offer letter generation, compensation negotiation, or digital signature.
     * Employee onboarding or background checks.
2. **Canonical Job Posting Rule:**
   * A `Job Posting` is the company's job description.
   * Do not create or introduce a separate "Corporate JD" entity or database table.
3. **Question Bank & Evaluation Settings Independence:**
   * A Job Posting holds at most one current core-question bank Blueprint. The owning Recruiter can view and edit core questions within this bank.
   * Posting-level evaluation weights are configured separately by the Recruiter and are not embedded into the question bank.
4. **Approval and Interview Presentation Lock:**
   * A Recruiter must select the company 3D interviewer model and Voice Profile before submitting a Job Posting for Admin approval.
   * Only `APPROVED` Job Postings with `OPEN` intake are discoverable and accept applications.
   * A Job Posting interview always uses its locked company 3D model and Voice Profile. The Candidate cannot override them.
5. **Immediate Application & CV Visibility:**
   * The Application and uploaded CV/resume exist and are immediately visible to the owning Recruiter upon submission.
   * The former rule requiring a completed technical interview before the application becomes visible is **superseded**.
6. **Two Distinct Decisions (CV Screening vs. Final Verdict):**
   * **CV Screening Decision:** Passing CV screening grants the candidate eligibility to launch the required technical interview. It does **not** constitute an application approval.
   * **Final Decision:** Rendered only after reviewing the candidate's interview performance evidence alongside their application and CV.
7. **Interview Slot Capacity & Intake Accounting:**
   * `job_postings.interview_slot` represents exclusively maximum recruitment interview capacity (the maximum number of Candidates that may be approved to proceed into the recruitment interview stage).
   * It does **NOT** represent total Applications/CVs submitted, company hiring headcount, or final hires. RoleCue does **NOT** persist or enforce company hiring headcount.
   * While intake is `OPEN`, Candidates may submit an **unlimited** number of Applications/CVs (submissions are not limited by `interview_slot`).
   * Recruiter may approve at most `interview_slot` Candidates to proceed to interview.
   * CV approval grants interview eligibility; it does **not** consume a funded slot yet.
   * Starting the technical interview consumes one funded slot strictly upon the Candidate's **first successful start**. Reconnecting to or resuming that active session does **not** consume another slot.
   * Candidates are **never** charged for recruitment interviews.
8. **Close Intake Semantics (No Reopen, No Manual End Recruitment):**
   * **Close intake** stops accepting new applications.
   * Closing intake automatically **REJECTS** all remaining unscreened or not-approved Applications.
   * Candidates already approved for interview remain interview-eligible and may still complete their interviews within their 24-hour windows.
   * Close intake does **not** refund interview slots and is **not** terminal Job Posting close.
   * There is **NO Reopen Intake flow** and **NO manual End Recruitment command**.
   * When the configured limitation (`interview_slot` approved candidates) is reached, the System Handler automatically executes Close Intake (*Close Job Posting When Meet Configured Limitation*).
9. **24-Hour Interview Deadline:**
   * When an Application is approved to proceed to interview:
     $$\text{interview\_deadline} = \text{cv\_approved\_at} + 24\text{ hours}$$
   * If the Candidate does not complete the recruitment interview by `interview_deadline`, the Application automatically transitions to terminal `REJECTED`.
10. **Hard-Gated Final Recruiter Review:**
    * Recruiter final Approve/Reject decisions are **HARD-GATED** until:
      $$\text{MAX}(\text{interview\_deadline})$$
      across all interview-eligible Applications for that Job Posting.
    * Before this timestamp, the Recruiter cannot render final Approve/Reject decisions.
    * After the gate, the Recruiter inspects Candidate profile, CV, and authorized interview scores, results, recordings, and transcripts inside **View Application Detail**, and renders final **Approve / Reject Application**.
    * All Recruiter CV, report, and recording review capabilities are consolidated inside **View Application Detail**.
11. **Terminal Job Posting Auto-Close & Unused Slot Refund:**
    * A Job Posting automatically reaches terminal closed state when **EVERY Application in scope reaches a terminal result** (`APPROVED` or `REJECTED`).
    * Terminal rejections include applications rejected during Close Intake cleanup, interview deadline expiry, Recruiter CV rejection, and final Recruiter rejection.
    * Upon terminal auto-close, the platform executes **Refund unused JP Candidate Slot**, refunding all still-unused recruitment interview capacity to the Recruiter's personal coin wallet as internal coins.
    * Refunds do **not** occur on Close Intake and do **not** call PayOS.
12. **Recruitment Recording & Visibility Boundaries:**
    * Audio/video recordings and transcripts captured during recruitment interviews are accessible exclusively to the owning Recruiter.
    * Candidates can view their overall recruitment evaluation score, but **cannot** access recruitment transcripts or audio/video recordings during recruitment.
    * Administrators do **not** have recruitment recording access by inference.
13. **Scores are Evidence for Human Review:**
    * Automated evaluation scores provide structured evidence to assist human hiring representatives. RoleCue does **not** make autonomous hiring decisions or automated disqualifications based on score thresholds.
14. **No Multi-Tenant Company Architecture:**
    * RoleCue does **NOT** implement multi-tenant SaaS architecture.
    * There are no Company tenants, company workspaces, tenant isolation middleware, or Row-Level Security tenant policies.
    * Job Postings are owned relationally by `recruiter_id`.
15. **Ownership & Access Isolation:**
    * Candidates can browse approved public Job Postings and view only their own submitted applications and recruitment scores.
    * Recruiters can only view and manage Job Postings and applications belonging to their own account.
16. **Final Decision Immutability:**
    * Once an application moves to `APPROVED` or `REJECTED`, the final decision cannot be reverted back to `PENDING`.

---

## 6. Relationships to Other Domains

* **[[01_Domains/Auth/README|Auth Domain]]:**
  Authenticates Candidates and Recruiters. Enforces role-based access control on posting management, CV screening, and application adjudication.
* **[[01_Domains/Job-Description/README|Job-Description Domain]]:**
  A Candidate browsing a Job Posting may copy its requirements to create a private **Target JD for Practice** in their personal library. However, the Job Posting and Target JD remain completely decoupled entities.
* **[[01_Domains/Administration/README|Administration Domain]]:**
  Administrators view, filter, and approve or reject Job Postings to ensure platform content quality.
* **[[01_Domains/Interview/README|Interview Domain]]:**
  Runs the required technical interview using the Job Posting's locked company 3D interviewer model and Voice Profile. Consumes one prepaid interview slot upon first successful start. Produces the Interview Result and recordings attached to the Application.
* **[[01_Domains/Payment/README|Payment Domain]]:**
  Debits Recruiter wallet coins to fund interview capacity (`interview_slot`). Upon terminal Job Posting auto-close, calculates eligible unused slots and credits internal coin refunds to the Recruiter's personal wallet (`Refund unused JP Candidate Slot`).

---

## 7. External Integrations

* **Email Provider:** Dispatches email notifications to Recruiters when new applications arrive, and notifies Candidates of CV screening outcomes and final application decisions.
