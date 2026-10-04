# Archive index

Back to [[00 Home]] · Every task log in `archieve/` (per AGENTS.md each has `# Progress` and `# Checked/Verified`).
Paths are relative to the repo root.

## Product & frontend
| Log | Topic |
|---|---|
| frontend-01 | All early frontend notes: plan, MVP, UX review + fixes, iRun look, real intervals.icu data, onboarding polish, training questions |
| frontend-06 | Frontend Dockerfile + compose service |
| frontend-07 | Landing restyle (gradient hero) |
| frontend-08 | Half‑finished registration resumes the right step |
| frontend-09 | Route page (MapLibre, route-map client, mock) |
| frontend-10 | Icons on section titles and stat tiles |
| frontend-11 | Theme switch System / Light / Dark |
| frontend-12 | Preferences page |
| frontend-13 | Profile sharing UI (`/u/:id`) |
| frontend-14 | AI Plan page (calendar, edits, contract) |
| frontend-15 | AI coach page refresh |
| frontend-16 | AI Plan on the shipped `/events` API |

## Backend & integration
| Log | Topic |
|---|---|
| auth-01 | JWT auth, registration, athlete profile, secrets to env |
| backend-01 | SQLAlchemy ORM + Postgres test fixtures |
| backend-02 / 03 / 04 | intervals.icu SQL schema, ORM models + DTOs, tests |
| backend-05 | camelCase API mapper |
| backend-06 | Endpoints for the frontend `Api`, intervals.icu import |
| backend-07 | Spec: per‑user ORS connector + route-map proxy |
| backend-08 | Public/private profile sharing |
| backend-09 | Run streak (+ plan rhythm from the engine) |
| backend-10 | Events CRUD + `POST /events/plan` |
| backend-11 | Real plan generator (ai-coach `/plan` → calendar events) |
| integration-01 | Frontend ↔ backend wiring plan (contract) |
| integration-02 … 05 | Contract paths, demo seed + read‑only, HTTP client, nginx proxy + e2e |
| integration-06 | User‑pasted ORS key |
| seed-01 | 10 demo users with planner‑made events |
| sample-data-01 | Sample export aligned with real intervals.icu fields |

## Coach & route
| Log | Topic |
|---|---|
| ai-01 | Coach first cut: extract/apply/reply, safety, debrief |
| ai-call-01 | Voice call (ElevenLabs STT/TTS) |
| ai-call-02 | iRunny in the site behind an email whitelist |
| map-01 | route-map service: loops, weather, crowd, main roads |

## ML
| Log | Topic |
|---|---|
| planning-01 | **The single active ML backlog** + model‑owner handoff |
| running-data-01 | **The single running‑data pipeline log** |
| ml-01 | Consolidated implementation history |
| ml-09 | Consolidated audits |
| ml-17 / 18 / 19 | Remote: pipeline features, terrain/GAP, run_ww real histories |
| ml-20 | Docs + dependency consolidation (archive map) |
| ml-21 / 22 / 23 | Historical audit, repair proposal, ranking research |
| ml-24 / 25 / 26 | Correctness fixes, foundation audit, repairs |
| ml-27 | Planning benchmark (simulator) |
| ml-28 / 29 / 30 | Shared contracts, running‑only scope, backlog cleanup |
| ml-31 | Models imitate the rules (benchmark audit) |
| ml-32 | Outcome models: speed, distance, break risk |
| ml-33 | Backend `PlanGenerator` port |

## Ops, debugging, verification
| Log | Topic |
|---|---|
| devops-01 | git flow setup |
| devops-02 / 04 / 05 | ML branch integration, PR #37, conflict resolution |
| ci-01 … 04 | Frontend tests in CI, local DB tests, secret scanning, deploy pipeline |
| compose-01 … 03 | Permission fix, drop server deploy, route-map container |
| env-01 | New env vars via `gen_env.sh` |
| git-01 / 02 | Release squashes into `main` |
| build-01 | Full rebuild |
| debug-01 … 06 | Test env, autofill "requests", UTC walk bug, location errors, old DB, CI seed uuid |
| verify-01 / 02 | Whole‑stack verification, feature checklist audit |
| vault-01 | This vault |
