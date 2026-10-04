# Infrastructure

Back to [[00 Home]] · Logs: frontend-06, ci-01…04, compose-01…03, env-01, devops-01…05, git-01/02, build-01

## Docker Compose
One file, five services (diagram in [[System overview]]):

| Service | Image / build | Health check |
|---|---|---|
| `db` | `postgres:16-alpine`, volume `./data/postgres`, `scripts/sql` as init dir | `pg_isready` |
| `api` | `backend/Dockerfile` (python 3.12 slim, non‑root) | `GET /health` via python urllib |
| `ai-coach` | `ai-coach/Dockerfile`, **built from repo root** (copies `ml/`, `scripts/`, `ai-coach/`) | `GET /` |
| `route-map` | `route-map/Dockerfile`, `env_file: route-map/.env` (optional) | `GET /` |
| `frontend` | multi‑stage: node 22 build → nginx 1.27 on :3000 | `wget 127.0.0.1:3000` (not `localhost` — nginx is IPv4‑only) |

`depends_on: service_healthy` chains them, so `docker compose up --wait` really means "everything works".

### nginx (`frontend/nginx.conf`)
- `/` → static `dist/`, `try_files … /index.html` (SPA deep links).
- `/api/` → `http://api:8000/` (prefix stripped), `client_max_body_size 6m`, `proxy_read_timeout 90s` (voice).
- `/route-map/` → `http://route-map:8001/`, 60 s timeout (cold `/day` ≈ 20 s).

## Environment
- `.env.example` lists every variable; `bash scripts/gen_env.sh` creates `.env` or **appends only missing**
  variables, generating random secrets for empty ones. New setting = field in `config.py` + line in
  `.env.example` + rerun the script.
- Required: `POSTGRES_USER/PASSWORD/DB`, `JWT_SECRET`, `ENCRYPTION_KEY`. Optional: `ANTHROPIC_API_KEY`,
  `ELEVENLABS_API_KEY`, `ORS_API_KEY` (in `route-map/.env`), `SEED_DEMO`, `CORS_ORIGINS`.
- Frontend: `VITE_API_URL` (default `/api`), `VITE_API_MOCK=1` for the offline mock.

## CI (`.github/workflows/ci.yml`)
| Job | Runs |
|---|---|
| `lint` | ruff on Python |
| `test` | backend pytest against a Postgres service |
| `frontend-test` | `npm ci` + `vitest run` (Node 24) |
| `integration` | `docker compose up --wait db api`, then `pytest -m integration` (schema = ORM check) |

## Git flow
- `feature/*` → PR into **`develop`**; `develop` → `main` only for releases (squashed: git-01, git-02).
- Conventional commits (`feat(frontend): …`). One task per session; every task logs to `archieve/<area>-NN.md`
  with `# Progress` + `# Checked/Verified` (AGENTS.md).
- Server deploy (GHCR + SSH + `deploy.sh` with rollback) was built in ci-04 and then **removed** in compose-02:
  the project runs locally only.

## Common local issues
| Symptom | Cause | Fix |
|---|---|---|
| api restarting, `PermissionError` | host file mode 600 copied into image | `chmod -R a+rX` in Dockerfile (compose-01) |
| "Could not open the demo" | old `data/postgres` without new columns | wipe or `ALTER TABLE` (debug-05) |
| backend tests fail locally, green in CI | DB port not published / `TEST_DATABASE_URL` ignored | publish `127.0.0.1:5432`, read `.env` in conftest (ci-02, debug-01) |
| coach 503 in Docker | no `ai-coach` service in compose | added service + `COACH_URL` (ai-call-02) |
| CI db unhealthy | seed inserted int ids into a uuid column | let Postgres generate ids (debug-06) |
