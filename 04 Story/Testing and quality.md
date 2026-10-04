# Testing and quality

Back to [[00 Home]] · Numbers are quoted from the archive logs at the time they were recorded.

## By component
| Component | Tooling | Latest recorded | Source |
|---|---|---|---|
| Frontend | Vitest + Testing Library (jsdom), oxlint, `tsc -b`, Playwright walkthroughs | **44 files / 595 tests** | groups-01 |
| Backend | pytest on a throwaway Postgres schema, ruff; integration tests on the compose stack | **163 passed** | groups-01 |
| ML | pytest/unittest, ruff, strict mypy on the pipeline, plan generator `check` | **142 passed / 354 subtests**; 273 real requests / 3,026 sessions / **0 rule violations** | devops-05, devops-02 |
| AI coach | `test_coach.py` (fake model client), `live_check.py` (real model, 25 cases + 8 attacks) | **76 passed** | backend-11, ai-01 |
| Route map | pytest with canned ORS/Open‑Meteo/Overpass responses | **27 passed** | integration-06 |

## Practices
- **Contract tests:** `backend/tests/test_routes.py` freezes the route table that `frontend/src/api/http.ts` uses;
  integration test diffs ORM models against the real DB schema.
- **Test‑first** on risky logic: token refresh races (concurrent 401s → exactly one refresh), demo read‑only,
  plan save ordering with rollback.
- **Test‑quality audit** (frontend-09): 111 → 302 tests, found a weekly‑km rounding bug.
- **Browser walkthroughs:** `npm run walkthrough`, `plan-walkthrough.mjs` take screenshots of every state
  (light + dark).
- **Independent review rounds** after each phase (code review agents; findings tracked and fixed in the logs).
- **Live checks** where mocks can lie: real ORS loops, real intervals.icu key rejection, real Claude reading,
  real voice round trip, compose stack with curl through nginx.

## Bugs that only real runs caught
- Map blank in a real browser while unit tests passed (MapLibre worker + CSS) — frontend-09.
- Missing activity at 01:30 local time (UTC vs local date) — debug-03.
- Health check `localhost` → IPv6 → container never healthy — ci-04.
- Haiku mock route loops that didn't close and climbed 1,400 m on 5 km; the agent had loosened the tests —
  rewritten and tests tightened to < 1 m — frontend-09.
- "Request on every keystroke" was the iCloud Passwords extension, not our app — debug-02.
