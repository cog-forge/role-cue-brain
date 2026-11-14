---
title: Learning Roadmap Domain
tags:
  - domain
  - roadmap
aliases:
  - Learning Roadmap
---

# Learning Roadmap Domain

> [!important] Agreed 2026-11-07 — backend implemented and merged 2026-11-13; UI in progress
> Read [[01_Domains/Learning-Roadmap/Learning-Roadmaps-Spec|the detailed Vietnamese specification]] for the complete agreed flow, schema, scoring examples and acceptance scenarios. Section 13 of the spec holds the implementation notes, the decisions recorded on 2026-11-13 and the spec → task → test traceability.

> [!warning] Acceptance: pending KAN-357
> End-to-end acceptance has not been recorded, so the feature is **not** marked released. Update this note only when KAN-357 has evidence.

Each Candidate Role Profile owns one private Roadmap. Its row is created with the profile; ordered knowledge topics are generated only from user-confirmed requirements. Candidates practice generally or focus on one topic. Recruitment evidence never updates the Roadmap.

For each topic, take the latest result per distinct core question, then the five most recent distinct questions. Average their normalized Barem question scores. Fewer than five is provisional; five and >= 80 is achieved. Roadmap progress is the percentage of topics achieved; all topics must be achieved for completed. New results can lower the status. Generation readiness is separate from learning progress.

MVP adds roadmaps, roadmap_items and roadmap_assessments, plus question-topic and session-focus references. No archived_at, topic editor, manual completion, snapshot/version tables or independent score cache. FE renders a linear path by position without locking later topics. Regeneration is permitted only before the profile has any practice session and must replace topic/bank mapping consistently.

Related: [[01_Domains/Evaluation/Scoring-Rubrics-Spec|Barem scoring]], [[01_Domains/Job-Description/README|JD confirmation]], [[01_Domains/Interview/README|practice sessions]], [[03_Decisions/Product-Decisions|Decision 25]].

## Implementation snapshot (2026-11-14)

- **Schema:** migration 000035 — the three tables and two columns above, plus two plpgsql triggers that keep a question's topic and a session's focus inside the same Role Profile. Existing Role Profiles received a `pending` Roadmap row.
- **API:** `GET /role-profiles/{id}/roadmap`, `GET .../roadmap/items/{item_id}/history`, `POST .../roadmap/retry`, `POST .../roadmap/regenerate`, `POST /role-profiles/{id}/confirm` (now generates), and `POST /interviews` with `focus_item_id`. Contract: `api/docs/roadmap-api.md` in the application repo.
- **Generation:** confirm generates 4–8 topics, then a topic-mapped bank (at least five questions per topic), synchronously within a 200 s deadline; a 5-minute lease and a fence on `roadmaps.updated_at` protect against stale workers.
- **Practice:** general or focused start; an open practice session is resumed with its own focus (`resumed: true`); a short topic is topped up with one model call before the charge.
- **Progress:** computed on read from stored Barem question scores; `update_state` (`up_to_date`/`updating`/`delayed`) is filled by the evaluation hook and the `roadmap.catchup` System Handler job.
- **Decisions of 2026-11-13:** a delivered-but-unanswered core counts 0 (no marker column; a technical failure on the last shown question cannot be told apart, accepted); injection-flagged sessions are excluded; profiles that practiced before the Roadmap existed get none (`ROADMAP_NOT_RETRYABLE`); the 10-minute `delayed` threshold; no `completed_at` index; the two triggers; generation shares the `question_bank` AI route.
- **Open:** UI (KAN-353..355) and acceptance (KAN-357).
