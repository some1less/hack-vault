# Frontend

Back to [[00 Home]] · Folder `frontend/` · Logs: frontend-01 … frontend-15, integration-04/05

## Stack
React 19 · Vite · TypeScript · Tailwind v4 · shadcn/ui + Radix · react-router 8 · react-hook-form + zod ·
Recharts · MapLibre GL 6 · sonner (toasts) · lucide icons · Vitest + Testing Library (jsdom) · oxlint ·
Playwright walkthrough scripts. Served by nginx 1.27 in Docker.

## Structure
| Path | Role |
|---|---|
| `src/api/types.ts` | **The `Api` interface** + all shared types — source of truth for shapes |
| `src/api/http.ts` | `createHttpApi()` — fetch client, token refresh, error mapping |
| `src/api/mock.ts` (+ `mockPlan.ts`) | localStorage implementation of the same `Api` (`VITE_API_MOCK=1`) |
| `src/api/index.ts` | picks HTTP or mock |
| `src/api/routeMap.ts` (+ mock) | client for the [[Route map]] service |
| `src/lib/*` | pure, tested helpers: `metrics`, `format`, `training`, `plan`, `streak`, `route`, `theme`, `sharing` |
| `src/pages/*` | screens (see below) |
| `src/components/design/*`, `components/ui/*` | design system (DButton, Chip, ChoiceCard, Field, StatTile…) |
| `scripts/walkthrough.mjs`, `plan-walkthrough.mjs` | headless browser click‑throughs with screenshots |

## Routes
| Route | Page |
|---|---|
| `/` | Landing |
| `/login`, `/register` (`/signup`) | auth |
| `/onboarding/connect` → `/body` → `/questions` | onboarding, guarded: unfinished users are sent to the next step |
| `/dashboard` | streak card, stat tiles, weekly km, pace trend, wellness, activities table |
| `/plan` | AI Plan calendar (PR #43) |
| `/route` | Route planner (lazy‑loaded: maplibre ≈ 1 MB stays out of the main bundle) |
| `/coach` | iRunny chat / call (whitelist) |
| `/profile`, `/u/:id` | own profile, public profile (no auth) |
| `/preferences`, `/preferences/training` | body + training answers |
| `/settings/{profile,security,connections}` | account, password & email, intervals.icu / ORS / iRunny |

## Things worth showing
- **Mock‑first development:** the team built the whole UI against a typed `Api` + localStorage mock, then swapped
  in HTTP with zero page changes (integration-01 plan).
- **Token refresh done right** — tested: concurrent 401s → exactly one refresh; another tab's tokens reused.
- **Onboarding with smart defaults** from real data (goal from longest run, frequency from last 8 weeks).
- **AI Plan calendar:** month grid (Mon–Sun), past days show real runs as "Done", native drag‑and‑drop swap,
  session dialog with coloured type chips, "Edited" + "Revert to the plan", undo toast, optimistic saves
  chained in order with rollback on failure, optional start time.
- **Theme tokens:** every colour is a CSS token; dark mode incl. the map style.
- **Accessibility:** focus management, aria on meters/switches, state never shown by colour alone.

## Decisions
- Desktop‑first; phone layout is not a goal.
- Brand **iRun**, monochrome palette + gradient accents, Geist font.
- Demo account detected by email (`isDemo(user)`), since backend ids are UUIDs.

Verification numbers in [[Testing and quality]].
