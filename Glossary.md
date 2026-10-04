# Glossary

Back to [[00 Home]]

| Term | Meaning |
|---|---|
| **iRun** | Product brand of the app |
| **iRunny** | The AI coach persona (chat + voice) — [[AI coach iRunny]] |
| **intervals.icu** | Training platform with a public API; relays Garmin/Strava/Polar/Apple data; our only data source |
| **ORS** | openrouteservice — routing/geocoding API used by [[Route map]] |
| **Open‑Meteo** | Free weather + air‑quality API |
| **Overpass** | Query API for OpenStreetMap data (main roads) |
| **PAR‑Q** | Physical Activity Readiness Questionnaire — "yes" (heart, chest pain, dizziness) blocks the plan |
| **TrainingAnswers** | Onboarding answers = plan engine input (goal, days, long‑run day, time limits, pain, injury, PAR‑Q) |
| **process_and_plan** | ML entry point that returns a 28‑day plan — [[ML planner]] |
| **make_plan / check_plan** | Rule‑engine plan builder and validator in `ml/plan_rules.py` |
| **Preset** | Beginner run/walk plan (Couch‑to‑5K style) |
| **rules / rules_fallback / ml / blocked** | Plan sources: rule baseline, model failed → rules, model‑ranked, refused |
| **Cutback week** | Planned lighter week after build weeks |
| **Taper** | Volume reduction before a race |
| **Long‑run share** | Cap on the longest run as a fraction of weekly km |
| **10 % rule** | Weekly volume growth cap |
| **GAP** | Grade‑adjusted pace (pace corrected for hills) |
| **LTHR** | Lactate‑threshold heart rate |
| **HRV** | Heart‑rate variability (recovery signal) |
| **CTL / ATL / TSB** | Fitness (42‑day), fatigue (7‑day), form = CTL − ATL |
| **run_ww** | Public dataset of real runners' daily training (BMClab, CC BY 4.0) used for training |
| **Retrace share** | Fraction of a loop that runs back over itself (spurs) |
| **Demo account** | `jan@demo.run` — shared, read‑only sample athlete Jan Kowalski |
| **Mock mode** | `VITE_API_MOCK=1` — frontend runs fully on localStorage |
| **PlanGenerator** | Backend port (interface) the ML side implements |
| **Whitelist** | Emails allowed to use the coach (keys cost money) |
| **AGENTS.md** | Repo rule: every task logs to `archieve/<area>-NN.md` |
