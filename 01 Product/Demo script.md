# Demo script

Back to [[00 Home]] · Setup in [[Infrastructure]].

## Start the stack
```sh
bash scripts/gen_env.sh            # creates/updates .env from .env.example (random secrets)
# put ANTHROPIC_API_KEY / ELEVENLABS_API_KEY in .env for the coach, ORS key in route-map/.env for routes
docker compose up -d --build --wait
open http://localhost:3000
```
Offline / no backend: `cd frontend && VITE_API_MOCK=1 npm run dev` (localStorage mock of the whole API).

## Accounts
| Login | Password | Why use it |
|---|---|---|
| `jan@demo.run` | `demo1234` | "Explore the demo" button. Sample athlete Jan Kowalski (77 activities, 90 days wellness). Read‑only for account changes |
| `Kuba1234@kuba.com` | `kuba1234` | **Coach whitelist.** Seeded with a hard tempo run on 2 Oct (HR 181 avg vs max 189, pace fading, HRV down) — perfect for the coach story |
| `anna.wisniewska@example.com` … `ewa.jankowska@example.com` | `<name>-pass-NN` | 8 more seeded runners (see `archieve/seed-01.md`) |

Kuba and the 9 others come from `scripts/sql/seed.sql`, which only loads on an **empty** `data/postgres`.

## Suggested 5‑minute flow
1. **Landing** → "the problem" in one sentence ([[Pitch]]).
2. **Register** a fresh user → Connect step (show masked key, "Where do I find this?") → Body → 3 questions
   (point out pre‑filled "Suggested" answers and the PAR‑Q note). *Or skip with the demo button.*
3. **Dashboard** (Jan or Kuba): streak card, weekly km, pace trend, wellness.
4. **AI Plan**: Generate → a real 4‑week plan from the engine; drag a run onto another day; open a session,
   change distance → "Edited"; Regenerate → "Nothing new since this plan".
5. **Route**: plan 5 km from Rynek / Błonia → loop, % off‑street, weather, elevation; right‑click to finish elsewhere.
6. **AI coach** as Kuba:
   - Chat: "how was my last run?" → debrief (HR too high, slow down).
   - "aaa that was way too hard" → coach checks real runs → asks "keep or lighten?" → "lighter" → plan shrinks.
   - "my knee hurts" → "0 to 10?" → "6" → plan paused, see a physio.
   - Call: ring → iRunny greets → speak → spoken reply.
7. **Profile** → Make profile public → open the link in a private window.
8. Close with **"The AI never writes a session"** ([[Key decisions]]).

## Things that can go wrong live
- Route page: ORS quota / bad key → 503/502 message. Have a key in `route-map/.env`.
- Coach: missing Anthropic/ElevenLabs keys → 503 `COACH_UNAVAILABLE`.
- Old `data/postgres` from before new columns → login fails (`UndefinedColumn`). Fix: wipe `data/postgres` or
  `ALTER TABLE` (see debug-05).
