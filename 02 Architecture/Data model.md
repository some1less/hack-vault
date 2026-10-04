# Data model

Back to [[00 Home]] · Owner: [[Backend]]

Postgres 16. The schema is **plain SQL** in `scripts/sql/` (no Alembic): `init.sql` (app tables),
`intervals_schema.sql` (mirror of intervals.icu data), `seed.sql` (10 demo users). Postgres runs them **only on
an empty `data/postgres`** — after a schema change, wipe it or `ALTER TABLE` by hand (debug-05, debug-06).
SQLAlchemy 2.0 ORM models in `backend/app/infrastructure/db/models/` must stay in sync; an integration test
(`test_orm_models_match_real_schema`) diffs them against the real DB in CI.

```mermaid
erDiagram
    users ||--o| athletes : "body profile"
    users ||--o{ refresh_tokens : sessions
    athletes ||--o| intervals_athletes : "intervals.icu profile"
    intervals_athletes ||--o{ intervals_activities : runs
    intervals_activities ||--o{ intervals_activity_streams : "per-second"
    intervals_athletes ||--o{ intervals_wellness : "per day"
    intervals_athletes ||--o{ intervals_events : "planned workouts"
    intervals_athletes ||--o{ intervals_sport_settings : zones

    users {
        uuid id PK
        text email "unique on lower(email)"
        text password_hash "argon2id"
        text display_name
        text city
        text intervals_api_key_encrypted "Fernet, NULL = not connected"
        jsonb onboarding "TrainingAnswers, NULL = not answered"
        bool is_public "profile share link"
        date last_run_date "streak"
        int streak_days
    }
    athletes {
        uuid user_id PK
        text sex
        date date_of_birth
        numeric height_cm
        numeric weight_kg
        text experience "new|lt1|1to3|3plus"
    }
    refresh_tokens {
        uuid id PK
        text token_hash "SHA-256 only"
        timestamptz expires_at
        timestamptz revoked_at
    }
    intervals_activities {
        text id PK "e.g. i101476455"
        text type "Run, TrailRun, Walk, Ride..."
        timestamp start_date_local
        float distance
        int moving_time
        float average_heartrate
        float total_elevation_gain
        float gap "grade-adjusted pace"
    }
    intervals_wellness {
        date day PK
        float hrv
        float resting_hr
        float sleep_secs
    }
    intervals_events {
        uuid id PK
        text category "WORKOUT, RACE_A..."
        timestamp start_date_local
        text name
        float distance
        int moving_time
    }
```

## Notes
- `users.onboarding` holds the [[User journey]] answers (`TrainingAnswers`): goal, goalDate, startDate, runDays
  (0 = Monday), longRunDay, maxMinWeekday/Weekend, continuousRunMin, pain, injury6m, parq.
- intervals.icu tables are prefixed `intervals_` and keyed by intervals.icu ids; sync upserts them.
- `intervals_events` doubles as the **plan calendar**: the seed puts ML‑planner sessions there and
  `POST /events/plan` is meant to fill it ([[ML planner]]).
- The plan for the AI Plan page (`generated` + `sessions` + `basis`) has **no table yet** — see
  [[Known limits and backlog]].
- Sample data: `scripts/inicial data/intervals/*.parquet` (athlete "Jan Kowalski": 77 activities, 90 days
  wellness), aligned with a real intervals.icu pull (sample-data-01). Backend demo seed copies it from
  `backend/app/seed/sample/*.json`.
