---
title: Learning Roadmap Domain
tags:
  - domain
  - roadmap
aliases:
  - Learning Roadmap
---

# Learning Roadmap Domain

> [!important] Agreed 2026-11-07 — implementation pending
> Read [[01_Domains/Learning-Roadmap/Learning-Roadmaps-Spec|the detailed Vietnamese specification]] for the complete agreed flow, schema, scoring examples and acceptance scenarios.

Each Candidate Role Profile owns one private Roadmap. Its row is created with the profile; ordered knowledge topics are generated only from user-confirmed requirements. Candidates practice generally or focus on one topic. Recruitment evidence never updates the Roadmap.

For each topic, take the latest result per distinct core question, then the five most recent distinct questions. Average their normalized Barem question scores. Fewer than five is provisional; five and >= 80 is achieved. Roadmap progress is the percentage of topics achieved; all topics must be achieved for completed. New results can lower the status. Generation readiness is separate from learning progress.

MVP adds roadmaps, roadmap_items and roadmap_assessments, plus question-topic and session-focus references. No archived_at, topic editor, manual completion, snapshot/version tables or independent score cache. FE renders a linear path by position without locking later topics. Regeneration is permitted only before the profile has any practice session and must replace topic/bank mapping consistently.

Related: [[01_Domains/Evaluation/Scoring-Rubrics-Spec|Barem scoring]], [[01_Domains/Job-Description/README|JD confirmation]], [[01_Domains/Interview/README|practice sessions]], [[03_Decisions/Product-Decisions|Decision 25]].
