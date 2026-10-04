# Features

Back to [[00 Home]] · Status as of 2026‑10‑04 (`develop` @ fd9e566).

Legend: ✅ on develop · 🟡 partial / mock only · 🔀 in an open PR · ❌ not built

| Feature | Status | Where | Source log |
|---|---|---|---|
| Landing page (iRun brand, gradient hero) | ✅ | `pages/Landing.tsx` | frontend-05, frontend-07 |
| Register / login / logout, JWT + rotating refresh | ✅ | `/auth/*`, `api/http.ts` | auth-01, integration-01…05 |
| Demo account `jan@demo.run`, read‑only | ✅ | `app/seed/demo.py`, `domain/demo.py` | integration-03 |
| Onboarding: Connect intervals.icu → Your body → About you (3 screens) | ✅ | `pages/onboarding/*` | frontend-05, 07, 09 |
| intervals.icu import (90 days activities + wellness), sync on dashboard open | ✅ | `IntervalsService` | backend-06, debug-03 |
| Dashboard: weekly km, pace trend, records, wellness tiles, activities table | ✅ | `pages/Dashboard.tsx` | frontend-02, verify-02 |
| Run streak card (window from the plan's rhythm) | ✅ | `GET /me/streak`, `StreakCard.tsx` | backend-09 |
| Profile + public profile sharing (`/u/:id`) | ✅ | `GET /users/{id}`, `PublicProfile.tsx` | backend-08, frontend-13 |
| Preferences page (body + training answers, editable) | ✅ | `/preferences` | frontend-12 |
| Settings: account, password & security, connected services | ✅ | `/settings/*` | frontend-10, 12 |
| Theme: System / Light / Dark | ✅ | `lib/theme.ts` | frontend-11 |
| Route page: loop of planned km, weather, AQI, elevation, crowd, finish point | ✅ | `/route` + `route-map/` | map-01, frontend-09, compose-03 |
| Bring‑your‑own openrouteservice key | 🟡 key in browser localStorage; backend connector specced | `lib/orsKey.ts` | integration-06, backend-07 |
| AI coach iRunny — chat | ✅ (email whitelist) | `/coach`, `/coach/chat` | ai-01, ai-call-02 |
| AI coach iRunny — voice call (STT → coach → TTS) | ✅ (whitelist) | `CoachCall.tsx`, `/coach/voice` | ai-call-01, 02 |
| Coach page refresh (Chat/Call switch, suggestions) | ✅ (PR #43) | `pages/coach/*` | frontend-15 |
| Coach readiness nudge, safety tier, missed runs, travel, race results | ✅ (PR #52) | `/coach/readiness` | PR #52 |
| AI Plan page: 4‑week calendar, edit / drag‑swap / delete / regenerate / start time | ✅ PR #43, real plans via `/events` | `/plan` | frontend-14, frontend-16 |
| ML plan engine | ✅ rule engine served by ai-coach `POST /plan`; full `process_and_plan` (with models) not yet in the app | `ml/` | ml-01…33, backend-11 |
| Events CRUD + `POST /events/plan` port | ✅ CRUD + real plan generator (`EnginePlanGenerator`) | `routes/intervals_events.py` | ml-33, backend-10, backend-11 |
| 10 seeded demo users with planner‑made events | ✅ | `scripts/sql/seed.sql` | seed-01 |
| Planning benchmark (simulated runners) | ✅ tool, protocol margins pending | `ml/benchmark/` | ml-27 |
| Outcome models (speed / distance / break risk) from real histories | 🟡 in progress | `ml/outcome_model.py` | ml-32 |
| Groups / competitions, points, GPX/FIT upload, doctor documents | ❌ | — | verify-02 |

See [[Known limits and backlog]] for what's missing and why.
