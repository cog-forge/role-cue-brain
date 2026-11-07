---
title: Post-Interview Evaluation and Reporting Domain
tags:
  - domain
  - evaluation
  - scoring
  - rubrics
aliases:
  - Evaluation Domain
  - Reporting Domain
---

# Post-Interview Evaluation & Reporting

RoleCue turns interview answers into criterion evidence, metric assessments and an overall score for human review. Scores have no automated hiring authority.

> [!important] Product direction vs implementation
> Decision 24, agreed 2026-11-07, retains metrics/weights and introduces reusable rubrics plus question-specific expected answers. **The rubric feature is not implemented yet.** Read [[01_Domains/Evaluation/Scoring-Rubrics-Spec|the detailed spec]] for the worked example and proposed technical details. The five seeded metrics remain the baseline catalog, not an immutable list.

## 1. Core distinctions

| Concept | Meaning | Storage in target design |
|---|---|---|
| Metric | Assessment dimension, e.g. Technical Accuracy | metrics |
| Rubric | Reusable scoring guide by question type | rubrics |
| Criterion / crit | Observable item, max points and scoring guide; belongs to one metric | rubric_criteria.metric_id |
| Expected answer | Question-specific reference concepts/acceptable alternatives; not exact-match keywords | core_questions.expected_answer TEXT |
| Question score | Sum of criterion points normalized to 100, for question detail | proposed interview_questions.score |
| Session metric score | Mean of that metric across questions that measure it | score_details.score |
| Overall | Weighted aggregate of session metric scores | interviews.score |

A question references exactly one rubric; many questions can share it. A rubric contains multiple crits; every crit references exactly one metric. Rubric/criterion points are real calculation inputs. Feedback/evidence explains why those points were awarded. The sum for a whole question is not the overall formula.

## 2. Calculation — read this before changing the scorer

```text
criterion points per question
  → normalize grouped criteria to each question's metric score
  → average each metric across relevant core questions
  → apply effective posting metric weights
  → overall
```

Example from the spec: Q01 crits 2/2 + 2/3 in Technical Accuracy produce 80; 3/5 in Problem-Solving produces 60. Q02 produces 60 and 40. Session metrics are therefore 70 and 50. With weights 60% and 40%, overall is 62. Question totals are 70 and 50; their average is 60 and **must not replace overall 62**.

Within a question/metric, divide summed earned points by summed maximum points; do not average individual criterion percentages without their point scales. Follow-ups belong to their core question and do not create extra score weight. Do not assign zero merely because a question does not measure a metric. Coverage/sampling, missing-answer and precision policies requiring engineering review are listed in the spec.

## 3. Posting weights stay in place

Decision 13a remains effective: custom subsets total 100%; absent custom rows use equal-split defaults on read (currently five metrics at 20% each), never persisted default rows. Using defaults is valid and does not itself block approval. Rubric readiness/metric coverage is an additional check, not a demand for custom weights. Editing weights is allowed in pending/rejected and locked from approval/open onward.

## 4. Rubric authoring and lifecycle

- Admin curates system templates. Recruiter may create private templates from scratch or copy permitted templates, then assign to own questions.
- Generation proposes allowed rubric_id and expected_answer alongside question text. Recruiter reviews in groups, changes exceptions and confirms preparation before submission.
- Two new configuration tables are agreed: rubrics and rubric_criteria. A separate criterion-result table is an engineering proposal needed for durable score/evidence detail, not another configuration/snapshot table.
- No snapshot/version system is in MVP. Definitions, question content, expected answers and bindings become immutable when referenced by an approved posting or started interview. Changing a used template means authoring another template for eligible drafts.
- Hard-delete only unreferenced data; archive prevents new assignments and preserves existing ones. Private templates are scoped by owner, never shared by guessing UUIDs.

## 5. Results and visibility

- interviews.score: one overall per interview; interviews.feedback: summary.
- score_details: one session metric score per (interview_id, metric_id), never a per-criterion row.
- conversation_turns.feedback: feedback by turn.
- Planned criterion results preserve points/evidence; proposed interview_questions.score holds explanatory question totals.
- Candidate owns full practice report access. For recruitment the Candidate gets **status only**, not even overall, and no crit/metric scores, expected answers, transcripts, recordings or derived roadmap scores. This corrects the older Brain wording to the later product decision and application behavior.
- Owning Recruiter sees recruitment evidence in Application Detail. Admin system-template curation does not grant access to private recruitment evidence or authority to alter historical scores.

## 6. Implemented baseline and planned delta

The current application already calls a model per core question plus follow-ups, averages metric outputs, verifies evidence and persists aggregate metric/overall scores. It does not persist per-core numeric outputs. The new feature changes the source of those scores to rubric criteria; it must preserve existing per-core isolation, bounded concurrency/retries, claims, budgets, redaction and final application-decision gates.

No completed results are recalculated because configurations change. Disposable development data may be reset when implementing this change; no legacy-data backfill/compatibility mode is required. That permission does not itself reset a database or authorize changing unrelated user work.

## 7. Related domains

- [[01_Domains/Evaluation/Scoring-Rubrics-Spec|Detailed spec and complete example]]
- [[01_Domains/Interview/README|Interview]]: core assignments, turns, follow-ups and evaluation execution.
- [[01_Domains/Job-Posting-Application/README|Recruitment]]: authoring, weights and consolidated application review.
- [[01_Domains/Administration/README|Administration]]: system rubric catalog with narrowly defined permissions.
- [[02_System/Domain-Model|Domain model]] and [[02_System/Data-Relationships|relationships]]: implemented baseline vs planned schema extension.
- [[03_Decisions/Product-Decisions|Product decisions]]: Decisions 13a and 24.

## Planned Topic Evidence Consumer — Decision 25

The agreed [[01_Domains/Learning-Roadmap/Learning-Roadmaps-Spec|Roadmap extension]] consumes normalized practice question scores from the Barem result, retaining the latest result per core question then the five most recent distinct questions for each topic. Topic mean>=80 with five questions is achieved; this does not replace the weighted metric overall or reveal recruitment scores. Roadmap integration requires activating Barem per-core persistence for practice. Explicit delivered skips receive 0; unasked or technically failed evidence is excluded. A saved interview result remains valid if Roadmap linking must retry after failure.
