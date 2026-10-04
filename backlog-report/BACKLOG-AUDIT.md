# Backlog Audit Report — FA26SE292 (RoleCue)

**Product:** RoleCue — AI-powered 3D technical interview simulation platform  
**Capstone Project Code:** FA26SE292  
**Workbook:** `SEP490_Backlog_FA26SE292_completed.xlsx`  
**Date:** 2026-09-27  

---

## 1. Source Authority & Verification

- **Primary Source Report:** `/home/dorriss/Documents/SEP490/production_report/Report7_Final_Project_Report.docx`
- **Folder Comparison:**
  - Checked `/home/dorriss/Documents/SEP490/backlog-report/` and `/home/dorriss/Documents/SEP490/production_report/`.
  - The `backlog-report/` directory contained only the initial template spreadsheet `SEP490_Backlog_471.SE2030.xlsx`.
  - `production_report/Report7_Final_Project_Report.docx` was confirmed as the sole, authoritative, finalized project report.
- **Foundational References:**
  - Formal Use Case catalog (Section III.2.2, Tables 6–11)
  - Business Rules catalog (Section III.5.1, Table 15)
  - Detailed Functional Requirements (Section III.3.2–3.6.3)
  - System Screens & Non-Screen Functions (Section III.3.1.2–3.1.4, Tables 12 & 14)
  - Ratified Product Decisions (`03_Decisions/Product-Decisions.md`)

---

## 2. Functional Requirements Coverage (`G06_Chí Bảo`)

- **Formal Use Cases Found in Report:** 57 Use Cases (UC-01 through UC-57)
  - *Registered User / Authentication (Table 6):* UC-01 to UC-07 (7 UCs)
  - *Candidate (Table 7):* UC-08 to UC-32 (25 UCs)
  - *Recruiter (Table 8):* UC-33 to UC-40 (8 UCs)
  - *Administrator (Table 9):* UC-41 to UC-54 (14 UCs)
  - *Guest (Table 10):* UC-55 to UC-56 (2 UCs)
  - *System Handler (Table 11):* UC-57 (1 UC)
- **Functional Backlog Rows Produced:** 57 rows (Rows 11 to 67 in sheet `G06_Chí Bảo`)
- **Use Case Coverage Result:** **PASS (100% — 57 / 57)**
- **Mapping Breakdown by Module:**
  1. *Public & Authentication:* 5 rows (`FR-PUB-01`, `FR-PUB-02`, `FR-AUTH-01`, `FR-AUTH-02`, `FR-AUTH-03` → UC-55, UC-56, UC-01, UC-02, UC-03)
  2. *Account & Access Management:* 4 rows (`FR-ACC-01`, `FR-ACC-02`, `FR-ACC-03`, `FR-ACC-04` → UC-04, UC-05, UC-06, UC-07)
  3. *Personal 3D Avatar:* 1 row (`FR-AVA-01` → UC-08)
  4. *AI Technical Interview:* 8 rows (`FR-INT-01` to `FR-INT-08` → UC-09, UC-10, UC-13, UC-14, UC-15, UC-16, UC-17, UC-57)
  5. *Target JD Management:* 5 rows (`FR-TJD-01` to `FR-TJD-05` → UC-11, UC-12, UC-23, UC-24, UC-25)
  6. *Interview Results & History:* 5 rows (`FR-RES-01` to `FR-RES-05` → UC-18, UC-19, UC-20, UC-21, UC-22)
  7. *Job Application:* 9 rows (`FR-APP-01` to `FR-APP-09` → UC-26, UC-27, UC-28, UC-29, UC-30, UC-31, UC-38, UC-39, UC-40)
  8. *Recruitment & Job Posting:* 5 rows (`FR-REC-01` to `FR-REC-05` → UC-33, UC-34, UC-35, UC-36, UC-37)
  9. *Platform Administration:* 11 rows (`FR-ADM-01` to `FR-ADM-11` → UC-41 to UC-51)
  10. *Membership & Payment:* 4 rows (`FR-PAY-01` to `FR-PAY-04` → UC-32, UC-52, UC-53, UC-54)
- **Traceability:** Every functional requirement explicitly cites its source formal Use Case ID, governing Business Rules, and report section in `Evidence Link / Note`.

---

## 3. Business Rules Coverage (`G06_Rules`)

- **Final Business Rules Found in Report:** 56 rules (BR-01 through BR-56 in Section III.5.1, Table 15)
- **Business Rule Backlog Rows Produced:** 56 rows (Rows 11 to 66 in sheet `G06_Rules`)
- **Business Rule Coverage Result:** **PASS (100% — 56 / 56)**
- **Review Decisions & Deprecations Verified:**
  - `BR-44`: Retained as Action Enabler (*Recruiter must review and confirm extracted Job Posting before submitting for Administrator approval*).
  - *Old BR-53 (Avaturn iframe responsibilities as BR):* Confirmed REMOVED.
  - *Old BR-54 (non-dependency on Avaturn Pro API as BR):* Confirmed REMOVED.
  - *Old BR-56 (redundant GLB-to-VRM conversion error):* Confirmed REMOVED.
  - `BR-53`: Contains ratified final wording (*Newly generated personal 3D avatar becomes available only after RoleCue completes format conversion*).
  - `BR-54`: Contains ratified final wording (*Candidate personal avatar is accessible only to its owning Candidate*).
  - `BR-55`: Contains ratified final wording (*Interview proceeds to evaluation when engine determines no further Question is required*).
  - `BR-56`: Contains ratified final wording (*Target JD practice interview avatar/voice selection constraints*).
- **Rule Taxonomy Used:** Constraint, Action Enabler, Action Disabler, Computation, Derivation, Inference.
- **Traceability:** Every Business Rule is mapped to its `Related Req ID`(s), Condition/Trigger, System Action, Exception/Error Message, Test Case, and Report Section.

---

## 4. Canonical Screen Merging Decision

- In accordance with architectural review decisions, the three previously separate Administrator calibration interfaces:
  - *Feature Configuration*
  - *AI Behaviour Management*
  - *Evaluation Criteria Calibration*
- Are consolidated to the canonical merged screen:
  - **Screen Name:** `Interview Configuration`
  - **Route:** `/admin/interview-config`
  - **Badge:** `ADM-08 • PRIMARY`
- Referenced by `FR-ADM-05` and `FR-ADM-06` under UI/API/Page.

---

## 5. Fields Left `TBD`

In strict adherence to project quality guidelines (no speculative invention):
- **Priority:** Set to `TBD` for all 57 functional rows (the finalized report does not specify MoSCoW priorities).
- **Owner:** Set to `TBD` for all 57 functional rows (the report assigns leadership/supervisor roles, not individual backlog row owners).
- **Status:** Set to `TBD` for all 57 functional rows and all 56 Business Rule rows (no external implementation tracking data was provided).
- **Header Metadata:** `Deployment Link` (`TBD`), `Sprint/Week` (`TBD`).

---

## 6. Stale Content Scan

- Executed automated full-text and XML-level scan across all cells and data validation lists in the workbook for stale terms:
  - `autowash`, `car wash`, `loyalty`, `credit package`, `credit wallet`, `candidate blueprint preview`, `avaturn pro api`, `swp-g05`, `swp-g06`.
- **Result:** **0 matches found. PASS.**
- All template AutoWash example rows (previously in rows 11–36 of `G06_Chí Bảo`) and outdated dropdown validation lists were replaced with RoleCue domain data.

---

## 7. Spreadsheet Integrity & Preservation

- **Original Workbook:** `/home/dorriss/Documents/SEP490/backlog-report/SEP490_Backlog_471.SE2030.xlsx` left **100% UNTOUCHED**.
- **Completed Deliverable:** `/home/dorriss/Documents/SEP490/backlog-report/SEP490_Backlog_FA26SE292_completed.xlsx`.
- **Modified Sheets:** Only `G06_Chí Bảo` and `G06_Rules`. Zero unrelated sheets touched.
- **Formulas:** Zero formula errors (0 formula cells in template; pure tabular backlog).
- **Formatting:** Preserved template layout, merged header cells (A1:K1, A8:K8), typography (Arial 10pt), alternating row shading (`FFEBF5FB` / `FFFFFFFF`), thin gridlines, text wrapping, and optimized column widths.
