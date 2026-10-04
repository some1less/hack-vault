# Known limits and backlog

Back to [[00 Home]] · Honest list, as of 2026‑10‑04. ML backlog source of truth: `archieve/planning-01.md`.

## Integration gaps (biggest first)
1. **AI Plan uses the rule engine, not the trained models.** Real plans now work end to end (backend-11:
   `EnginePlanGenerator` → ai-coach `POST /plan` → `plan_rules.make_plan`). Not yet: the full
   `process_and_plan` path with ML ranking. A blocked plan (PAR‑Q) returns `[]`, so the page shows "no
   sessions" instead of the BlockedCard with the reason.
2. **Coach is not per‑user.** One plan and conversation in memory, shared by everyone whitelisted, built from the
   sample athlete. Needed: per‑user plan/`asked` state, the user's real runs via `get_runs`, per‑user rate limits.
3. **ORS key in the browser.** backend-07 spec: `connectors` table (encrypted), `PUT/DELETE /connectors/{provider}`,
   api proxies `/route-map/*` and adds `X-ORS-Key`.

## ML
- Saved model bundle is incompatible with the current feature contract → rules fallback in practice.
- The intended **two learned optimizers (speed, distance)** are not delivered; outcome models (ml-32) in progress.
- Benchmark protocol margins await a decision (ml-27).
- Training window (16 weeks) ≠ serving window (12 weeks); `o_repairs` always empty; per‑row normalization
  diagnostics (planning-01 P2/P3).
- Persisted progression state (preset level, prior pain, block week) not yet versioned.
- Wellness‑based adaptation needs a policy.
- No proof of improved real‑athlete outcomes — model scores must not be shown as race‑success probabilities.

## Product / UX
- Route: `duration_min` is walking time; geocode has no focus point; loops can miss length by ~30 % in places.
- Streak: a run imported later with an older date isn't counted.
- Not built: groups/competitions, points/customisation, GPX/FIT upload, doctor‑document health history,
  weather‑adjusted targets.
- Phone layout (desktop‑only by decision).

## Ops
- No migrations — schema changes require a DB reset or manual `ALTER`.
- No server deployment (local Docker only).
