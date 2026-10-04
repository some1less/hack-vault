# Key decisions

Back to [[00 Home]] · Each line: decision → why → where it's recorded.

## Product
| # | Decision | Why | Log |
|---|---|---|---|
| D1 | **intervals.icu as the single data source** | one API key relays Garmin, Strava, Polar, Apple; permissive API terms | frontend-07, research |
| D2 | **The LLM never writes a training session** — it fills a small form; rules apply changes; `check_plan()` validates | generative coaches fail when the model invents numbers; safety | ai-01 |
| D3 | Raw health values never go to the model, only relative facts | privacy (GDPR Art. 9), fewer hallucinations | ai-01, research |
| D4 | PAR‑Q yes / "pain stops me" **blocks** the plan | not a medical device; wellness purpose only | frontend-09, ml |
| D5 | **Running only** (Run, TrailRun, VirtualRun) | focus; other sports polluted features | ml-29 |
| D6 | 4‑week plan, always starting on a Monday; plan gate ≥ 5 runs in 30 days | enough data for a personal plan; matches the engine | frontend-14, ml |
| D7 | Onboarding answers are exactly the plan engine's inputs, with smart defaults | fewest taps, no re‑entry later | frontend-09 |
| D8 | Desktop‑first UI | hackathon scope | frontend-12 |
| D9 | Coach behind an email whitelist | API keys cost money during the demo | ai-call-02 |

## Architecture
| # | Decision | Why | Log |
|---|---|---|---|
| A1 | Typed `Api` interface + localStorage **mock first**, HTTP later | frontend and backend could move in parallel; offline demo | frontend-01, integration-01 |
| A2 | Same‑origin `/api` proxy (Vite + nginx) | no CORS, identical code in dev and Docker | integration-01 |
| A3 | JWT access + **rotating** refresh with reuse detection; one shared refresh on the client | secure sessions without cookies | auth-01, integration-04 |
| A4 | Plain SQL schema, no migrations | speed during a hackathon (cost: manual DB resets) | backend-01 |
| A5 | Separate services for coach and route map | different stacks/owners; isolate external APIs and keys | map-01, ai-call-02 |
| A6 | Keys stay in the api; coach gets them per request | coach container is keyless; whitelist check first | ai-call-02 |
| A7 | Demo account seeded by the backend, read‑only | everyone can try it; nobody can lock it | integration-03 |
| A8 | Hexagonal port `PlanGenerator` in backend infra | ML team implements; backend doesn't depend on ML internals | ml-33 |
| A9 | Versioned data/feature contracts; reject incompatible model bundles, fall back to rules | never serve a model trained on different features | ml-28, ml-29 |
| A10 | Constraints → candidates → learned ranker → validation | models may rank, never widen safety limits | planning-01, ml-23 |
| A11 | Route profile `foot-hiking` + own main‑road scoring | ORS public weightings are constant | map-01 |
| A12 | No server deploy; local Docker only | removed unused GHCR/SSH pipeline | compose-02 |

## Process
- One task = one session = one archive log with Progress and Checked/Verified; reviewers verify claims.
- Independent reviews (separate agent, often stronger model) after each phase; tests written first where possible.
- Honest status: logs say "Not verified" explicitly — e.g. ML models imitate rules (ml-31) is reported, not hidden.
