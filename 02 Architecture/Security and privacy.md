# Security and privacy

Back to [[00 Home]] · Legal background in [[Research summary]]

## Authentication
- Passwords: **argon2id** (pwdlib). Login does a dummy hash check for unknown emails (no timing oracle);
  wrong password and unknown email return the same error.
- Access token: HS256 JWT, 15 min, `Authorization: Bearer`. Refresh token: opaque, **only SHA‑256 hash stored**,
  30 days, rotated on every use; **reuse of a revoked token revokes all sessions**. Password change → new tokens,
  other sessions signed out.
- Frontend keeps only `runner.tokens` in localStorage; one shared refresh in flight; multi‑tab aware.

## Secrets
- intervals.icu key: verified, then **Fernet‑encrypted** (`ENCRYPTION_KEY`) in `users`; never returned to the
  browser, never in localStorage (the HTTP client has a test for that).
- Anthropic / ElevenLabs keys: only in the api's env; sent to the coach **per request** as headers, after the
  whitelist check. The coach container has no keys.
- ORS key: today in the user's localStorage → `X-ORS-Key` (temporary); backend‑07 specs an encrypted
  `connectors` table + proxy so it leaves the browser.
- `JWT_SECRET`, `ENCRYPTION_KEY`, `POSTGRES_PASSWORD` are **required, no defaults** (`:?` in compose, config
  fails fast). `scripts/gen_env.sh` generates them. Lesson learned: early defaults leaked on a feature branch →
  history rewritten, secrets rotated (auth-01).
- Postgres published on `127.0.0.1` only. GitHub secret scanning has explicit, reviewed `paths-ignore` for fake
  test credentials (ci-03).
- Containers run as a non‑root `appuser`; `chmod -R a+rX` in Dockerfiles (compose-01).

## Shared demo account
`jan@demo.run / demo1234` is shared by every visitor, so email/password change, delete and intervals
(dis)connect return `403 DEMO_READ_ONLY` — nobody can lock others out.

## Coach safety (prompt‑injection & health)
- 500‑char cap, rate limits, red‑flag regex **before** any model call (chest pain, fainting, dizziness, short of
  breath → fixed "see a doctor" reply).
- The model only fills a small `Feedback` form; invalid fields are dropped; **code applies changes**;
  `check_plan()` rejects unsafe plans; runs never shrink below 60 %.
- The reply step never sees the user's raw message (no injection path into the reply) and never gets raw HR —
  only relative facts.
- Off‑topic / "ignore your instructions" → fixed redirect. `live_check.py` runs 8 attack cases against the real
  model.
- Access is behind an **email whitelist** for the hackathon (keys cost money).

## Health data (GDPR)
Research conclusion: treat the whole runner profile as **Art. 9 health data** — explicit consent, DPIA,
minimisation, processor contract for the LLM, AI Act Art. 50 chatbot disclosure, and a *wellness* purpose (not
diagnosis) to stay out of MDR. With voice, audio goes to ElevenLabs and text to Anthropic — consent text must say
so. Profiles are **private by default**; a private profile is indistinguishable from a missing one (404).
