# Research summary

Back to [[00 Home]] · Full notes in `research_notes/` (dated October 2026; external claims not re‑verified).

## Headline
**Build transparent baselines, fetch less, stay non‑medical.**

## Competitors share one engine
TrainingPeaks, intervals.icu, Garmin, COROS, Polar, WHOOP, Oura, Runalyze, Runna, Athletica — under the
branding they all run:
1. a per‑session **load score** relative to a personal threshold (rTSS, HRSS, TRIMP…),
2. a ~42‑day **fitness** and ~7‑day **fatigue** smoother (CTL/ATL/TSB),
3. short‑vs‑long **personal baselines** for HRV, resting HR, sleep,
4. **rule‑based plan changes**, mostly triggered by the user ("Not feeling 100 %", holiday mode).

Nobody publishes readiness weights, and no composite readiness score is peer‑validated. → **Transparency is the
differentiator.** Generative feedback from big vendors failed publicly when the model produced its own numbers.

Useful facts: with zero load, fitness (CTL, 1/42 update) halves in ~29 days; fatigue in ~5 days.
**Weather‑adjusted targets are an open gap** among running apps (only TriDot does it) — our [[Route map]]
weather is a first step.

## Small data → simple, robust methods
Tens to hundreds of runs per user: deterministic formulas, median/MAD baselines with smallest‑worthwhile‑change
bands, race predictions as intervals from published model error — **not** per‑user ML. This is why the
[[ML planner]] puts rules first and lets models only rank inside the rules.

## Legal (EU / Poland)
- The runner profile and inferences = **GDPR Art. 9 health data**: explicit granular consent, DPIA, minimisation,
  Art. 28 processor contract for the LLM (no training on data, EU / zero‑retention), **AI Act Art. 50** chatbot
  disclosure (applies since 2 Aug 2026).
- Keep a **wellness** purpose — predicting illness or treating injury would make it MDR class IIa software.
- intervals.icu API terms are permissive; upstream limits apply (some platforms' activities arrive as empty stubs;
  Garmin attribution must flow into charts/AI answers it influenced).

## How it shaped the build
| Research point | Design result |
|---|---|
| LLMs inventing numbers fail | [[AI coach iRunny]] only fills a form; code computes |
| Art. 9 health data | raw HR never sent to the model; profiles private by default; whitelist |
| Wellness, not medical | PAR‑Q / pain blocks with "see a doctor"; no diagnosis |
| Small data | rules + constraints first; models rank, never widen limits |
| Single relay source | intervals.icu as the only connector |
