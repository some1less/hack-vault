# API reference

Back to [[00 Home]] · Flows in [[Request flows]]

All JSON is **camelCase** (`ApiModel` alias generator). Errors are `{code, message}`. Browser path = `/api` +
path below (nginx/Vite strip `/api`). `test_routes.py` pins the full route table — it *is* the contract with
`frontend/src/api/http.ts`.

## Backend (`api`, FastAPI :8000)
| Method + path | Auth | What |
|---|---|---|
| `GET /health` | – | liveness |
| `POST /auth/register` | – | `{name,email,password}` → 201 User (then client logs in) |
| `POST /auth/login` | – | → `{accessToken, refreshToken, tokenType, expiresIn}` |
| `POST /auth/refresh` | – | rotate refresh token |
| `POST /auth/logout` | – | revoke refresh token |
| `GET /me` · `PATCH /me` · `DELETE /me` | JWT | current user; `{name, city, isPublic}` |
| `POST /me/email` · `POST /me/password` | JWT | need current password; password returns new tokens |
| `PUT /me/profile` | JWT | body profile (sex, DOB, height, weight, experience) |
| `PUT /me/onboarding` | JWT | `TrainingAnswers` |
| `GET /me/streak` | JWT | `{streakDays, lastRunDate, intervalDays, deadline, planDays, planSource}` |
| `GET /athlete` | JWT | intervals.icu athlete merged with body profile |
| `POST /intervals/connect` · `DELETE /intervals/connect` | JWT | verify + store key, import 90 days / disconnect |
| `POST /intervals/sync` | JWT | re‑import (dashboard calls it on open) |
| `GET /activities?from&to` · `GET /wellness?from&to` | JWT | stored data, oldest first, inclusive dates |
| `GET /users/{id}` · `GET /users/{id}/activities` | – | **public** profile; private = 404 (same as missing) |
| `GET /groups` · `POST /groups {name}` | JWT | my groups / create (creator = owner + first member) |
| `POST /groups/join {name}` | JWT | join by exact name (any case), idempotent |
| `GET /groups/{id}` | JWT | members ranked by streak (each streak refreshed with its own plan window); 404 for non‑members |
| `DELETE /groups/{id}/members/me` | JWT | leave; owner → longest member; last member deletes the group |
| `GET/POST /events` · `GET/PATCH/DELETE /events/{id}` | JWT | calendar events CRUD |
| `POST /events/plan` | JWT | next‑month plan from the plan engine (ai-coach `/plan`) → unsaved events (`id: null`); 503 `PLAN_UNAVAILABLE` if the engine is down |
| `GET /coach/access` · `GET /coach/greeting` · `POST /coach/chat` · `POST /coach/voice` | JWT + whitelist | forwarded to ai-coach |
| `GET /coach/readiness` | JWT + whitelist | recovery check (PR #52) |

### Error codes
`INVALID_CREDENTIALS`, `EMAIL_TAKEN`, `WRONG_PASSWORD`, `UNAUTHORIZED` (401, client logs out),
`INTERVALS_AUTH` (key rejected), `INTERVALS_UNAVAILABLE`, `DEMO_READ_ONLY` (403), `VALIDATION_ERROR`,
`NOT_FOUND`, `CONFLICT`, `NOT_WHITELISTED` (403), `COACH_UNAVAILABLE` (503), `PLAN_UNAVAILABLE` (503), `GROUP_NAME_TAKEN` (409), `GROUP_NAME_NOT_FOUND` (404), `GROUP_FULL`, `GROUP_LIMIT`. Frontend adds `UNKNOWN` for network
failures / non‑JSON bodies, and `NOT_ENOUGH_RUNS`, `ONBOARDING_REQUIRED` for the AI Plan contract.

## AI Plan contract (frontend PR #43, mock reference)
frontend-16 maps these methods onto `/events` inside `http.ts`, so the page keeps this shape.
| Api method | HTTP | Notes |
|---|---|---|
| `getPlan()` | `GET /me/plan` | 404 → `null` |
| `generatePlan(startDate)` | `POST /me/plan {startDate}` | `NOT_ENOUGH_RUNS` (< 5 runs in 30 d), `ONBOARDING_REQUIRED`; blocked is a normal Plan |
| `savePlanSessions(sessions)` | `PUT /me/plan/sessions` | full 28‑day list; `generated` stays untouched |

`Plan = {startDate, createdAt, source: ml|rules|rules_fallback|preset|blocked, level, blockedReason, basis,
generated[], sessions[]}`; `PlanSession = {date, type: rest|easy|long|tempo|intervals|race, distanceKm,
durationMin, paceLo/Hi, hrLo/Hi, walkRun, note, startTime}`.

## ai-coach (Flask :5000, internal)
| Call | What |
|---|---|
| `POST /chat {message}` | → `{text, asked, run, feedback, state}`; `asked` = `confirm_lighter` / `pain_score` / null |
| `POST /voice` (raw audio ≤ 5 MB) | STT → chat turn → TTS → `{transcript, text, audio}` |
| `GET /greeting` | cached "Hey, my name is iRunny" mp3 |
| `GET /runs/last` | last run + debrief, no model call |
| `POST /rhythm` | answers + runs → planned weekdays of week 1 (for the streak) |
| `POST /plan` | answers + runs → full plan `{level, blocked_reason, sessions}` (for `/events/plan`) |
| `GET /state`, `POST /reset` | test UI only |
Limits: 1–500 chars, 10 msgs/min, 100/day.

## route-map (FastAPI :8001)
| Call | What |
|---|---|
| `GET /day?lat&lon&distance_km&when[&end_lat&end_lon][&seed]` | `{route, weather}` — the one the app uses |
| `POST /route` | route only (loop or A→B) |
| `GET /weather` | Open‑Meteo forecast + air quality |
| `GET /geocode?q` | ORS geocoding |
| `GET /` | MapLibre test page |
`X-ORS-Key` header overrides env `ORS_API_KEY`. No key → 503; ORS rejects key → 401; quota → 502.
