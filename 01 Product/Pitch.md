# Pitch

Back to [[00 Home]]

## The problem
- Most training plans **never listen**. You have a bad week, the plan carries on.
- Runners already record everything (Garmin, Strava, Apple Watch…), but the data sits in charts nobody reads.
- "AI coaches" from big vendors have failed in public when the model **made up its own numbers**
  (see [[Research summary]]).
- Planning a route for "5 km easy today" means juggling a map, weather and a plan.

## Our answer: iRun
1. **Connect once** — paste an intervals.icu API key; it relays Garmin, Strava, Polar, Apple… We import
   90 days of runs and wellness (HRV, resting HR, sleep). → [[Backend]]
2. **Three quick questions** — goal, your week, health (PAR‑Q). Pre‑filled from your data. → [[User journey]]
3. **A personal 4‑week plan** — built by a rule engine grounded in running‑injury research, ranked by models,
   and validated before you see it. Editable as a calendar. → [[ML planner]], [[Frontend]]
4. **iRunny, a coach you can talk to** — chat or a real voice call. "That run killed me" → it checks your real
   runs, tells you honestly whether the data agrees, and changes the plan only if you say yes. → [[AI coach iRunny]]
5. **Today's route** — a loop of the planned distance from where you are, mostly off‑street, with weather,
   air quality, elevation and a crowd estimate. → [[Route map]]
6. **Stay consistent** — a run streak whose window comes from your plan's rhythm; share your profile by link.

## Why it's different
| Others | iRun |
|---|---|
| LLM writes the workout | **The AI never writes a session.** It only reads what you said; code + rules decide; `check_plan()` refuses anything unsafe |
| Black‑box readiness scores | Transparent rules you can explain (10 % weekly growth cap, cutback weeks, long‑run share, taper) |
| Heart rate sent to the model | **Raw health data never goes to the model** — only relative facts like "10 bpm over your easy range" |
| Plans that ignore pain | Pain 0–10 question; PAR‑Q / "pain stops me" **blocks** the plan and says "see a doctor" |
| Generic routes | Loops that match the planned km, 80–100 % footpath, avoid main roads and spurs |

## Who it's for
Recreational runners — from Couch‑to‑5K beginners (run/walk presets) to half‑marathoners — who already have a
watch or phone recording runs.

## One‑liners for slides
- "The AI understands you. The rules keep you safe."
- "Your data, your plan, your route — in one place."
- "Honest coaching: if your data says you were fine, iRunny tells you so."
