# Request flows

Back to [[00 Home]] · Services in [[System overview]], endpoints in [[API reference]].

## 1. Login and silent token refresh
Access token 15 min, refresh token 30 days, **rotated on every refresh**. Replaying an old refresh token signs
out every session (reuse detection). The frontend makes sure parallel 401s share **one** refresh.

```mermaid
sequenceDiagram
    participant UI as SPA (api/http.ts)
    participant N as nginx
    participant A as api
    participant D as Postgres
    UI->>N: POST /api/auth/login {email,password}
    N->>A: POST /auth/login
    A->>D: user by lower(email), argon2 verify
    A-->>UI: {accessToken, refreshToken}
    UI->>A: GET /me (Bearer access)
    Note over UI: later — 3 calls in parallel, access expired
    UI->>A: GET /athlete, /activities, /wellness
    A-->>UI: 401 ×3
    UI->>A: POST /auth/refresh (once, shared promise)
    A->>D: lock token row, revoke old, issue new
    A-->>UI: new pair
    UI->>A: retry the 3 calls
```
If another tab already refreshed (stored token ≠ the one that got 401) the client just retries with the stored one.

## 2. Connect intervals.icu and import
```mermaid
sequenceDiagram
    participant UI as SPA
    participant A as api
    participant I as intervals.icu
    participant D as Postgres
    UI->>A: POST /intervals/connect {apiKey}
    A->>A: ensure_not_demo
    A->>I: GET /athlete/0 (Basic API_KEY:key)
    I-->>A: 200 athlete / 401
    A->>I: GET activities + wellness (oldest = today-90d, newest = today+1)
    A->>D: encrypt key (Fernet) → users, upsert intervals_* rows
    A-->>UI: ImportSummary {athleteId, activities, runs, wellnessDays, from, to}
    Note over UI,A: Dashboard calls POST /intervals/sync on every open (failures ignored)
```
`newest = today + 1` because intervals.icu filters by the athlete's **local** date while the server runs in UTC
(debug-03: a 01:30 local walk was missing).

## 3. Coach chat / voice
```mermaid
sequenceDiagram
    participant UI as SPA /coach
    participant A as api /coach/*
    participant C as ai-coach
    participant M as Anthropic
    participant E as ElevenLabs
    UI->>A: POST /coach/chat {message} (JWT)
    A->>A: require_whitelisted(email) else 403 NOT_WHITELISTED
    A->>C: POST /chat + X-Anthropic-Key
    C->>C: red-flag regex (chest pain, fainting…) → fixed reply
    C->>M: extract() → Feedback form (tool use)
    C->>C: apply() rules → make_plan / scale → check_plan()
    C->>M: phrase() reply from facts only
    C-->>A: {text, asked, run, feedback, state}
    A-->>UI: same
    Note over UI,E: Voice: browser records (MediaRecorder, ≤60 s) → POST /coach/voice<br/>→ ElevenLabs STT → same turn() → ElevenLabs TTS → {text, audio mp3}
```

## 4. Route for today
```mermaid
sequenceDiagram
    participant UI as SPA /route
    participant R as route-map
    participant O as openrouteservice
    participant W as Open-Meteo
    participant P as Overpass
    UI->>R: GET /route-map/day?lat&lon&distance_km&when (+X-ORS-Key)
    par loops
        R->>O: round_trip foot-hiking ×3 seeds (asks 1.18× length)
    and roads
        R->>P: motorway/trunk/primary/secondary ways near start
    end
    R->>R: keep loops within 10 % of km, retrace < 3 %, rank by main-road share
    R->>W: forecast + air quality for `when`
    R-->>UI: {route: geojson+stats+crowd, weather}
```

## 5. Run streak (plan rhythm)
```mermaid
sequenceDiagram
    participant UI as Dashboard
    participant A as api
    participant C as ai-coach /rhythm
    UI->>A: GET /me/streak
    A->>A: read onboarding answers + 90 days runs
    A->>C: POST /rhythm (5 s timeout)
    C->>C: plan_rules.features + make_plan → weekdays of week 1
    C-->>A: planDays e.g. [1,3,6]
    A->>A: intervalDays = longest gap between planned weekdays, refresh streak
    A-->>UI: {streakDays, lastRunDate, intervalDays, deadline, planDays, planSource}
```
If the engine is down, it falls back to the user's picked run days, then to a default of 3.

## 6. AI Plan (target design)
```mermaid
sequenceDiagram
    participant UI as SPA /plan
    participant A as api /events
    participant G as PlanGenerator (port)
    participant C as ai-coach /plan
    UI->>A: POST /events/plan
    A->>A: answers from users.onboarding + last 31 days of events
    A->>G: EnginePlanGenerator.generate_next_month_plan
    G->>G: + 90 days of runs and date of birth from the db
    G->>C: POST /plan (30 s timeout)
    C->>C: plan_rules.features + make_plan → sessions
    C-->>G: {level, blocked_reason, sessions}
    G-->>A: rest days skipped, sessions → IntervalsEvent
    A-->>UI: unsaved events (id null) → calendar
    UI->>A: POST/PATCH/DELETE /events/{id} for edits
```
Events use the seed format ("Easy run 4.9 km", a colour per type, pace/HR lines in the description), so the
calendar reads seeded and generated plans the same way. If the engine is down → 503 `PLAN_UNAVAILABLE`.
Live check (backend-11): Kuba → 200 in 0.04 s, 12 events.
