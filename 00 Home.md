# iRun — Hack Vault

Knowledge base for **iRun**, our HackYeah 2026 running app. Start here, follow the links.
Open the folder `hack-vault/` as an Obsidian vault (wikilinks + Mermaid render there; GitHub renders them too).

> One line: **iRun turns your real runs from intervals.icu into a safe, personal 4‑week plan, a coach you can
> talk to (chat or voice), and a route for today's run — with transparent rules, not black‑box guesses.**

## Map

### Product — what and why
- [[Pitch]] — problem, solution, why it's different
- [[Features]] — every feature and its status
- [[User journey]] — the path a new runner takes through the app
- [[Demo script]] — logins and a click-by-click demo for the presentation

### Architecture — how it fits together
- [[System overview]] — services, ports, who talks to whom (diagram)
- [[Request flows]] — sequence diagrams of the important calls
- [[Data model]] — Postgres tables
- [[API reference]] — every endpoint
- [[Security and privacy]] — tokens, secrets, demo lock, health data

### Components — how each part works
- [[Frontend]] — React app
- [[Backend]] — FastAPI + Postgres
- [[ML planner]] — the plan engine (rules + models)
- [[AI coach iRunny]] — chat & voice coach
- [[Route map]] — loop routes, weather, crowd
- [[Infrastructure]] — Docker, nginx, CI, env

### Engineering story
- [[Key decisions]] — the choices that shaped the project
- [[Testing and quality]] — how we verified things
- [[Known limits and backlog]] — honest list of what's not done
- [[Research summary]] — market and legal research behind the design
- [[Timeline]] — how the project grew, task by task
- [[Archive index]] — every `archieve/*.md` log in one line
- [[Glossary]] — terms and abbreviations

## Repo map

| Folder | What lives there | Note |
|---|---|---|
| `frontend/` | React 19 + Vite SPA, nginx image | [[Frontend]] |
| `backend/` | FastAPI API, auth, intervals.icu import, Postgres | [[Backend]] |
| `ml/` | Running-data pipeline, rule engine, models, benchmark | [[ML planner]] |
| `ai-coach/` | iRunny coach (Flask): chat, voice, debrief, rhythm | [[AI coach iRunny]] |
| `route-map/` | Route + weather service (FastAPI) | [[Route map]] |
| `scripts/` | SQL schema + seed, sample intervals.icu export, env generator | [[Data model]] |
| `research_notes/` | Competitor, methods, legal research | [[Research summary]] |
| `archieve/` | Task logs (Progress / Checked/Verified) | [[Archive index]] |
