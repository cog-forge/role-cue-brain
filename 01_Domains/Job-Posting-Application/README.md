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
  * **Question Bank & Settings:** Generates a single internal core-question bank Blueprint. The owning Recruiter can view and edit core questions for their own posting. The Recruiter also configures posting-level evaluation weights separately from the question bank.
  * **Interview Presentation:** Company 3D interviewer model and Voice Profile selected by the Recruiter before Admin approval. Candidates cannot override either setting for a recruitment interview.
  * **Lifecycle Status:** `PENDING_APPROVAL`, `APPROVED`, `REJECTED`, `ARCHIVED`, `RECRUITMENT_ENDED`.
  * **Intake Control (`intake_status`):** `OPEN` or `CLOSED`.
* **Application Intake (Open vs. Close):**
  * `Open intake`: Actively accepts new candidate applications and CV submissions.
  * `Close intake`: Temporarily stops accepting new applications. **Critical distinction:** Applicants who already passed CV screening prior to closing intake can still conduct their interview. Close intake does **not** refund interview slots or terminate recruitment. The Recruiter can reopen intake at any time.
* **Interview Slot (`interview_slots`):**
  A unit of prepaid interview capacity funded by the Recruiter using coins from their personal wallet and allocated to an approved Job Posting.
  * Submitting an application or passing CV screening does **not** consume a slot.
  * Starting the interview consumes one funded slot. Reconnecting to or resuming that active session does **not** consume another.
  * Unused funded slots are refunded as coins back into the Recruiter's personal wallet upon **End Recruitment**.
* **Application (`applications`):**
  A candidate submission to an approved Job Posting containing candidate profile information and an uploaded CV/resume.
  * **Immediate Recruiter Visibility:** The Application and CV are immediately visible to the owning Recruiter upon submission, prior to any interview taking place. (The former assumption requiring a completed interview prior to application visibility is superseded).
* **Two Distinct Decisions:**
  1. **CV Screening Decision:** The Recruiter's initial evaluation of the uploaded CV/resume (`Pass` or `Reject` CV screening). Passing grants the candidate eligibility to conduct the required technical interview. It does not constitute a hiring offer or final acceptance.
  2. **Final Decision:** The definitive, terminal verdict rendered by the Recruiter (`Approve` or `Reject` Application) after reviewing the candidate's complete profile, CV, technical Interview Result, and recruitment audio/video recordings and transcripts.
* **Interview Result & Recruitment Recordings:**
  The persisted performance report produced by the Job Posting interview, accompanied by recorded audio/video streams and conversational transcripts.
  * Attached to the Application for the owning Recruiter's review.
  * **Role-Specific Disclosure:** Candidates can view their overall recruitment evaluation score, but **cannot** view recruitment transcripts or audio/video recordings during recruitment.
* **End Recruitment:**
  The formal conclusion of recruitment on a Job Posting. Only ending recruitment calculates eligible unused funded interview slots and refunds them as coins back to the owning Recruiter's personal coin wallet.

---

## 3. Actors Involved

* **Recruiter:**
  * Creates Job Postings from JD-like content; reviews and confirms AI-extracted structured requirements.
  * Views and edits core questions in the question bank (Blueprint) for own Job Postings.
  * Configures posting-level evaluation weights across technical competencies.
  * Selects the company 3D interviewer model and Voice Profile before submitting for Admin approval.
  * Funds interview slots for the Job Posting using coins from their personal wallet.
  * Controls application intake: Open intake or Close intake.
  * Views incoming Applications and CVs immediately upon submission.
  * Performs CV screening (`Pass` or `Reject` CV screening).
  * Reviews completed applicant dossiers, including technical evaluation scores, radar breakdowns, and recruitment audio/video recordings and transcripts.
  * Renders definitive final decision: **Approve** or **Reject** Application.
  * Ends recruitment on filled/closed postings to reclaim eligible unused interview slots as coins.
* **Candidate:**
  * Browses and searches approved public Job Postings with open intake.
  * Views Job Posting details and submits application with uploaded CV/resume.
  * Tracks application status.
  * Upon passing CV screening, participates in the required technical interview using the Job Posting's locked presentation and funded slot (Candidate is not charged).
  * Views overall recruitment evaluation score (transcripts and recordings remain hidden).
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

    Note over Recruiter,Admin: 1. Posting Setup, Blueprint & Approval
    Recruiter->>System: Create Job Posting from JD-like content
    System->>System: AI extracts technical requirements
    Recruiter->>System: Review & confirm requirements
    System->>DB: Generate single Question-Bank Blueprint
    Recruiter->>System: View/edit core questions & set evaluation weights
    Recruiter->>System: Select company 3D model and Voice Profile
    Recruiter->>System: Submit Job Posting for approval
    System->>DB: Store Job Posting (Status: PENDING_APPROVAL)
    Admin->>System: Approve Job Posting
    System->>DB: Update Job Posting Status to APPROVED

    Note over Recruiter,Pay: 2. Slot Funding & Intake Control
    Recruiter->>Pay: Fund Interview Slots using Coins from Wallet
    Pay->>DB: Allocate funded Interview Slots to Job Posting
    Recruiter->>System: Open Application Intake (Intake: OPEN)
    System->>DB: Set intake_status = OPEN

    Note over Candidate,Recruiter: 3. Application Submission & Immediate Visibility
    Candidate->>System: Browse Approved Postings (Intake: OPEN)
    Candidate->>System: Select Apply & Upload CV/resume
    System->>DB: Create Application with CV (Status: PENDING_CV_SCREENING)
    System-->>Recruiter: Application & CV immediately visible in Recruiter Dashboard
    System->>Mail: Notify Recruiter of incoming application
    System-->>Candidate: Confirm application submitted

    Note over Recruiter,Candidate: 4. CV Screening Decision
    Recruiter->>System: Inspect candidate application & uploaded CV
    alt Recruiter Rejects CV
        Recruiter->>System: Reject CV
        System->>DB: Update Application (Status: CV_REJECTED)
        System->>Mail: Send CV Rejection notice to Candidate
    else Recruiter Passes CV
        Recruiter->>System: Pass CV
        System->>DB: Update Application (Status: CV_PASSED - Eligible to Interview)
        System->>Mail: Send Interview Eligibility notice to Candidate
    end

    opt Candidate passed CV screening
        Note over Candidate,System: 5. Recruitment Technical Interview
        Candidate->>System: Start recruitment interview
        System->>DB: Consume 1 funded Interview Slot (Prepaid by Recruiter)
        Note over Candidate,System: Runs locked company model & Voice Profile; captures video/audio & transcript
        System->>DB: Store Interview Result & recruitment recordings
        System->>DB: Attach Interview Result & recordings to Application
        System-->>Candidate: Display overall score (Recordings/transcripts hidden)

        Note over Recruiter,Candidate: 6. Recruiter Review & Final Decision
        Recruiter->>System: Review candidate dossier, CV, evaluation result, and recordings/transcript
        alt Recruiter Approves Application
            Recruiter->>System: Approve Application
            System->>DB: Update Application Status to APPROVED
            System->>Mail: Send Final Approval notice to Candidate
        else Recruiter Rejects Application
            Recruiter->>System: Reject Application
            System->>DB: Update Application Status to REJECTED
            System->>Mail: Send Final Rejection notice to Candidate
        end
    end

    Note over Recruiter,Pay: 7. Intake Management vs. End Recruitment
    opt Close Intake (Temporary Halt)
        Recruiter->>System: Close intake (Intake: CLOSED)
        Note over Recruiter,System: Stops new applications; already-screened applicants can still interview. No slot refund.
    end
    opt End Recruitment (Conclusion)
        Recruiter->>System: End Recruitment
        System->>DB: Mark recruitment finished; calculate eligible unused interview slots
        System->>Pay: Refund eligible unused slots as Coins to Recruiter Wallet
        Pay->>DB: Credit Coins to Recruiter Wallet (CoinTransaction)
        System-->>Recruiter: Unused slot refund completed
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
7. **Interview Slot Accounting & Boundaries:**
   * Interview slots represent prepaid interview capacity, funded by the Recruiter using coins from their personal wallet.
   * Submitting a CV or passing CV screening does **not** consume an interview slot.
   * Starting the technical interview consumes one funded slot. Reconnecting to or resuming that session does **not** consume another.
   * Candidates are **never** charged for recruitment interviews.
8. **Intake Control (Open/Close) vs. End Recruitment:**
   * **Close intake** temporarily halts receiving new applications. Candidates who already passed CV screening can still conduct their interview. Close intake does **not** refund interview slots.
   * **End Recruitment** marks recruitment complete for the posting. Only End Recruitment calculates eligible unused funded interview slots and refunds them as coins back into the Recruiter's personal wallet.
9. **Recruitment Recording & Visibility Boundaries:**
   * Audio/video recordings and transcripts captured during recruitment interviews are accessible exclusively to the owning Recruiter.
   * Candidates can view their overall recruitment evaluation score, but **cannot** access recruitment transcripts or audio/video recordings during recruitment.
   * Administrators do **not** have recruitment recording access by inference.
10. **Scores are Evidence for Human Review:**
    * Automated evaluation scores provide structured evidence to assist human hiring representatives. RoleCue does **not** make autonomous hiring decisions or automated disqualifications based on score thresholds.
11. **No Multi-Tenant Company Architecture:**
    * RoleCue does **NOT** implement multi-tenant SaaS architecture.
    * There are no Company tenants, company workspaces, tenant isolation middleware, or Row-Level Security tenant policies.
    * Job Postings are owned relationally by `recruiter_id`.
12. **Ownership & Access Isolation:**
    * Candidates can browse approved public Job Postings and view only their own submitted applications and recruitment scores.
    * Recruiters can only view and manage Job Postings and applications belonging to their own account.
13. **Final Decision Immutability:**
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
  Runs the required technical interview using the Job Posting's locked company 3D interviewer model and Voice Profile. Consumes one prepaid interview slot upon start. Produces the Interview Result and recordings attached to the Application.
* **[[01_Domains/Payment/README|Payment Domain]]:**
  Debits Recruiter wallet coins to fund interview slots. Upon End Recruitment, calculates eligible unused slots and credits coin refunds to the Recruiter's personal wallet.

---

## 7. External Integrations

* **Email Provider:** Dispatches email notifications to Recruiters when new applications arrive, and notifies Candidates of CV screening outcomes and final application decisions.
