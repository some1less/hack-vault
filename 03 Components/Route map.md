# Route map

Back to [[00 Home]] · Folder `route-map/` · Logs: map-01, frontend-09, integration-06, compose-03, debug-04

## What it does
Given a start point, a planned distance and a time: returns a **running loop** of about that length, drawn on a
map, with elevation, % off‑street, % on main roads, a crowd estimate, and the **weather + air quality** for that
hour. Optional finish point (A→B).

## How the loop is chosen
1. Ask openrouteservice for round trips with the **`foot-hiking`** profile (90–99 % footpaths, avoids steps;
   `foot-walking` hugged the street grid, cycling profiles were 40–79 % street).
2. ORS loops come back ~15 % short and noisy → ask for **1.18×** the distance, try up to **5 random seeds**
   (3 in parallel first), keep one within **10 %** of the plan.
3. Reject loops that run back over themselves (**retrace share > 3 %**, route cut every 10 m, 12 m tolerance).
4. Fetch motorway/trunk/primary/secondary ways from **Overpass** (mirror fallback, cached 1 h) and prefer loops
   with the lowest share within 25 m of a main road.
5. Crowd score = road‑type share + time of day (an estimate, labelled as such).
6. Weather from **Open‑Meteo** (temp, feels‑like, rain %, wind, UV, AQI, PM2.5); `weather: null` if it fails.

## In the app
- `/route` page: start = athlete's city (geocoded) → GPS → search → map click; distance, start time;
  "New loop" for another seed; right‑click = finish point ("Direct route: 1.5 km of the planned 5 km").
- MapLibre with OpenFreeMap tiles (positron / dark). Lazy‑loaded chunk.
- Mock client for `VITE_API_MOCK=1` (deterministic synthetic loop that really closes on the start).

## Keys
`ORS_API_KEY` in `route-map/.env`, or the user's own key from Settings → Connected services sent as
`X-ORS-Key` (validated by a geocode call before saving). Next step: backend stores it encrypted and proxies
(backend-07).

## Lessons
- MapLibre worker URL + CSS `position: relative` broke the map in a real browser while all unit tests passed —
  fixed with `setWorkerUrl` and `size-full` (frontend-09).
- ORS `quiet`/`green` weightings and `noise`/`green` extras are constant on the public server → useless for
  ranking; we built our own main‑road scoring instead.
- `duration_min` is walking time from the hiking profile, not running time (open).
- Geocode has no focus point ("Błonia" found Gdańsk first) (open).
