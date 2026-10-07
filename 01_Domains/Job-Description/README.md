---
title: Job Description Domain
tags:
  - domain
  - job-description
  - extraction
  - refinement
  - target-jd
aliases:
  - JD Domain
  - Target JD Domain
---

# Job Description Domain

The **Job Description (JD) Domain** governs the ingestion of raw target job descriptions, AI-powered structured technical competency extraction, candidate review, and natural-language refinement prior to interview planning.

---

## 1. Purpose

Transform unstructured, heterogeneous job postings (provided by Candidates) into normalized, verified technical requirement profiles. It ensures that subsequent interview simulation plans are tightly aligned with specific employer expectations while giving candidates fine-grained control over their practice focus.

---

## 2. Core Concepts

* **Target JD for Practice (`job_descriptions`):** A job description uploaded or pasted by a Candidate to structure a mock interview simulation. Owned exclusively by that candidate.
* **Extracted JD Schema:** The structured technical competency payload extracted by the AI engine:
  * `title`: Target role title (e.g., "Backend Engineer", "Cloud Architect").
  * `seniority_level`: Inferred or candidate-selected seniority (`intern`, `junior`, `mid`, `senior`, `lead`).
  * `skills`: Categorized technical skill items:
    * `category`: `programming_language`, `framework`, `database`, `tool`, `technology`, `other`.
    * `requirement`: `required` or `preferred`.
  * `technologies`: Specific software packages, libraries, or runtimes (e.g., "Kafka", "Docker", "Redis").
  * `domain_knowledge`: Specialized industry domains (e.g., "Fintech", "Distributed Systems").
* **Candidate Refinement Notes:** Natural-language instructions provided by the candidate during review (e.g., *"Exclude C# from the interview"*, *"Focus on distributed caching and concurrency"*). Stored alongside the reviewed requirements.
* **Confirmed Technical Requirements:** The final, candidate-verified requirements state. Human confirmation of extracted requirements authorizes internal question-bank generation (`core_questions`).

---

## 3. Actors Involved

* **Candidate:** Ingests raw target JD text or PDF files; reviews and modifies extracted skill tags; provides natural-language refinement notes; confirms finalized requirements; manages their personal Target JD library.

---

## 4. Main Domain Flow

```mermaid
flowchart TD
    A["Raw Target JD<br/>(Pasted Text or PDF Upload)"] --> B["Sanitization & Normalization<br/>(Clean UTF-8, whitespace, page ordering)"]
    B --> C["AI Structured Extraction<br/>(LLM Semantic Extraction)"]
    C --> D["Deterministic Validation<br/>(Schema checks, tag deduplication, category validation)"]
    D --> E["Candidate Interactive Review<br/>(Adjust seniority, edit tags, toggle required/preferred)"]
    E --> F["Candidate Refinement Notes<br/>(Natural-language instructions: e.g., 'Exclude C#')"]
    F --> G["Candidate Confirms Requirements<br/>(Persist Confirmed Requirements)"]
    G --> H["Trigger Internal Blueprint Generation<br/>(Single Core-Question Bank — Hidden from Candidate)"]
```

### Flow Details:
1. **Ingestion:** The candidate submits raw text or uploads a PDF. Text is extracted in logical reading order and normalized into clean text.
2. **AI Extraction:** The normalized plain text is submitted to an LLM provider using structured extraction templates.
3. **Deterministic Validation:** The raw output is verified by backend validation rules:
   * Title and competency collections must be present and non-empty.
   * Case-insensitive duplicate skills are merged; conflicting category assignments fail validation.
   * Soft skills (interpersonal traits, punctuality) are explicitly stripped or rejected.
4. **Interactive Candidate Review:** The candidate inspects extracted skills, changes the seniority badge, deletes unneeded items, and adds missing technologies.
5. **Refinement Notes:** The candidate supplies free-form natural language notes to steer the upcoming interview focus.
6. **Requirement Confirmation:** The candidate explicitly confirms the reviewed requirements. Confirmation is the mandatory gate before question-bank generation.
7. **Single Question-Bank Generation:** The confirmed requirements and refinement notes are used to generate the internal core-question bank (`core_questions` table, conceptually referred to as the Interview Blueprint). Exactly one current core-question bank exists per JD, which remains strictly hidden from the candidate.

---

## 5. Business Rules & Invariants

1. **Target JD vs. Job Posting Distinction:**
   A Target JD is a candidate's personal practice resource. It is **not** a Recruiter Job Posting. Candidates cannot publish their Target JDs to the public job board, and Recruiters cannot view Candidate Target JDs.
2. **Candidate Ownership Scoping:**
   Target JDs are private. Every read, update, list, and delete query is strictly filtered by the authenticated `user_id`.
3. **Seniority vs. Difficulty Independence:**
   A role's `seniority_level` (`intern` to `lead`) describes job seniority, **not** mock interview session difficulty (`easy`, `medium`, `hard`). A candidate preparing for a `senior` JD can configure an `easy` diagnostic warm-up or a `hard` high-stress simulation.
4. **Technical Competencies Exclusivity:**
   Extraction and interview planning focus exclusively on technical domain skills. Behavioral attributes and soft skills are out of scope.
5. **Human Confirmation Precedes Question Bank Generation:**
   Candidate review and explicit confirmation of extracted requirements and refinement notes must occur *before* the system generates the core-question bank (`core_questions`). Once generated, the requirements snapshot is locked for that bank.
6. **Hidden Question Bank Rule (Candidate Confirms Requirements, Not Questions):**
   The Candidate **never** views, edits, or confirms the Interview Blueprint / question bank. There is no blueprint preview capability. The Candidate confirms only the extracted requirements.
7. **Single Current Question Bank per JD:**
   A Target JD possesses at most **one current core-question bank** in `core_questions` (conceptual Blueprint; it is absent until generated). Do not generate multiple current banks just because session configuration or difficulty differs.
8. **Deletion Safety:**
   Deleting a Target JD from the library must not corrupt historical completed interview sessions or performance reports associated with it.

---

## 6. Relationships to Other Domains

* **[[01_Domains/Interview/README|Interview Domain]]:**
  The Confirmed Requirements and Refinement Notes serve as the direct input to internal **question bank generation** (persisted in `core_questions`, conceptually the Interview Blueprint). Exactly one current core-question bank exists per Target JD (absent until generated); changing session configuration or difficulty does not create multiple current banks.
* **[[01_Domains/Auth/README|Auth Domain]]:**
  Every Target JD references `user_id` to establish candidate ownership.
* **[[01_Domains/Job-Posting-Application/README|Job-Posting-Application Domain]]:**
  Candidates may choose to copy text from a public Job Posting to create a Target JD for practice, but they remain separate entities with separate lifecycles.

---

## 7. External Integrations

* **LLM Provider:** Executes structured extraction prompts to parse unstructured raw text into valid JSON competency schemas.
