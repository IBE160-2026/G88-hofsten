---
title: 'VoyageAI Simulated Cost Model'
type: 'chore'
created: '2026-11-17'
status: 'done'
route: 'oneshot'
review_loop_iteration: 0
context: []
---

<frozen-after-approval reason="human-owned intent — do not modify unless human renegotiates">

## Intent

**Problem:** `product-brief.md` leaves the cost elements open and does not state the approved simulated fuel model and test voyage, so the brief cannot yet provide a consistent basis for later requirements and tests.

**Approach:** Keep `product-brief.md` at product level with a concise explanation of the approved alternative A and its comparison table. Preserve the detailed simulated inputs, formulas, exact intermediate arithmetic, and hand-checked results in this implementation spec. The model calculates fuel cost for sailing and berth-window waiting, excluding fuel during the departure delay and port stays, port fees, demurrage, and other complex costs. In the approved sample, the slow-down scenario targets the next feasible opening, 4 June at 06:00. Preserve the existing structure and relevant content; do not change code or other existing files, and do not commit or push.

The hand-checkable test inputs are: A–B 600 nautical miles; B–C 300 nautical miles; scheduled departure from A on 1 June at 00:00 and actual departure at 12:00 after a 12-hour delay; 10-knot planned speed; 12 tonnes/day reference fuel use at 10 knots; 1 tonne/day while waiting; and simulated fuel price of USD 600/tonne. Berth windows at B are 3 June 06:00–18:00 and 4 June 06:00–18:00; the 12-hour port stay starts on arrival at opening or during an open window, waits until opening if early, or waits for the next listed window if arriving at or after closing, and may finish after closing. Scenario A keeps 10 knots, scenario B uses 12.5 knots on A–B, and scenario C targets the 4 June 06:00 opening (the next feasible opening) at 100/11 knots on A–B; all use 10 knots on B–C. Fuel use per day is 12 × (v/10)^3, where v is speed in knots. If a target speed falls outside the inclusive 8–13-knot bounds, it is marked infeasible, not clamped or reported as meeting the window.

</frozen-after-approval>

## Implementation Notes

- Updated `product-brief.md` with the concise approved model summary and comparison table; this spec retains the detailed inputs, formulas, and hand-checked results.
- In this spec, “alternative A” means the simple fuel-cost model; “Scenario A” means keeping planned speed.
- Implemented the approved model summary in `product-brief.md` and retained detailed inputs, formulas, exact intermediate arithmetic, and hand-checked results here.
- Reused the existing review rounds and checked findings against the current files; no new review round was started. Exact calculation checks and document diagnostics passed; commit is pending user approval of the file list.

### Calculation rules

- Sailing time (hours) = distance (nautical miles) / speed (knots).
- Daily fuel at speed `v` knots = `12 * (v / 10)^3` tonnes/day. A day is 24 hours; partial-day fuel is prorated linearly.
- Sailing fuel = daily fuel * sailing hours / 24.
- Waiting fuel at B = `1 * waiting hours / 24` tonnes. Fuel during the 12-hour delay at A and the 12-hour port stay at B is excluded.
- Total modelled fuel = A-B sailing fuel + B berth-window waiting fuel + B-C sailing fuel.
- Fuel cost = total modelled fuel * USD 600/tonne. The same price applies to all modelled fuel. Calculations use exact fractions or unrounded values; displayed fuel uses round-half-up to three decimals and costs use round-half-up to cents.
- ETA calculations and berth-window eligibility use unrounded elapsed times; displayed ETAs are rounded half-up to the nearest minute. The exact `100/11`-knot sample value is used in calculations; `9.09` knots is display-only.
- Berth windows are start-inclusive and end-exclusive. An early arrival waits for opening; arrival at opening or during a window starts the 12-hour stay; arrival at or after closing waits for the next listed window. The stay may finish after closing. This fixture has only the two listed windows; behavior after the final window is outside this hand-check. Timestamps use one timezone-naive simulated clock; year and timezone conversion are not modeled.
- The 8-13-knot limits apply to each sailing leg. The 3 June 06:00 opening would require `600/42 = 100/7` knots (about 14.286) to reach exactly at opening, above the 13-knot limit. The speed-up scenario instead arrives within that window at 12:00. The slow-down scenario targets the next opening after the 10-knot arrival, 4 June 06:00; its exact `100/11`-knot target is within the limits. If a target is outside the limits, mark it infeasible; do not clamp it or report it as meeting the window. The three speeds and target window are values for this sample fixture, not general selection rules for other voyages.

### Hand-checked scenarios

**1. Keep 10 knots**

- A-B: `600/10 = 60` hours = `5/2` days; fuel = `12 * 5/2 = 30` tonnes. Depart A 1 June 12:00; arrive B 4 June 00:00.
- Wait for the 4 June 06:00 window: 6 hours; waiting fuel = `1 * 6/24 = 1/4` tonne. The 12-hour stay ends 4 June 18:00.
- B-C: `300/10 = 30` hours = `5/4` days; fuel = `12 * 5/4 = 15` tonnes; ETA C = 6 June 00:00.
- Total = `30 + 1/4 + 15 = 181/4 = 45.25` tonnes; cost = `181/4 * 600 = USD 27,150`.

**2. Increase A-B to 12.5 knots**

- A-B: `600/12.5 = 48` hours = 2 days. Daily fuel = `12 * (12.5/10)^3 = 375/16` tonnes/day; A-B fuel = `375/16 * 2 = 375/8 = 46.875` tonnes. ETA B = 3 June 12:00, within the window; wait and waiting fuel = 0.
- The 12-hour stay ends 4 June 00:00. B-C fuel = 15 tonnes; ETA C = 5 June 06:00.
- Total = `375/8 + 15 = 495/8 = 61.875` tonnes; cost = `495/8 * 600 = USD 37,125`.

**3. Slow A-B to target 4 June 06:00**

- From 1 June 12:00 to 4 June 06:00 is 66 hours = `11/4` days. Speed = `600/66 = 100/11` knots (about 9.090909); the exact fraction is used in calculations.
- Daily fuel = `12 * (10/11)^3 = 12000/1331` tonnes/day; A-B fuel = `12000/1331 * 11/4 = 33000/1331` tonnes (about 24.793388). ETA B = 4 June 06:00; wait and waiting fuel = 0.
- The 12-hour stay ends 4 June 18:00. B-C fuel = 15 tonnes; ETA C = 6 June 00:00.
- Total = `33000/1331 + 15 = 52965/1331 = 4815/121` tonnes (about 39.793388); cost = `4815/121 * 600 = 2889000/121` USD (about USD 23,876.033058).

## Review Triage Log

- `false` — Adversarial: hand calculations were reported missing; the three scenarios above show sailing times, per-leg fuel, berth wait, stay end, ETA C, totals, and exact costs.
- `false` — Adversarial: per-leg fuel calculations were reported missing; each scenario gives A-B and B-C fuel, and the keep-speed case includes waiting fuel.
- `false` — Adversarial: unrounded fuel totals and cost multiplication were reported missing; exact fractions and USD 600/tonne calculations are recorded above, with display rounding defined.
- `false` — Adversarial: the 4 June target was reported unexplained; the brief specifies the next B window after the 10-knot ETA, while the 3 June opening precedes that ETA. The spec also records why an exact 3 June 06:00 arrival exceeds the speed limit.
- `false` — Adversarial: time basis was reported unspecified; timestamps use one timezone-naive simulated clock, with year/timezone conversion excluded.
- `false` — Adversarial: day length and partial-day fuel were reported unspecified; a day is 24 hours and partial days are prorated linearly.
- `low` (rejected) — Adversarial/Structure: further condensation was suggested; the brief now keeps only the approved model rules, exclusions, sample summary, and comparison table, so more cuts risk obscuring requested behavior without fixing a concrete error.
- `false` — Adversarial: the `updated` date was reported stale; the Product Brief frontmatter is dated 2026-11-17.
- `false` — Adversarial: conflicting review status notes were reported; the current implementation notes state one consistent state, with review complete and commit awaiting approval.
- `false` — Adversarial: review provenance was reported absent; this triage log records the findings and their dispositions.
- `low` (rejected) — Edge-case: behavior after the final listed window is outside the approved two-window hand-check; no scenario in this fixture arrives after the final window, so adding a general fallback would expand the current model scope.
- `low` (rejected) — Structure: the model paragraph could be shorter; the current paragraph preserves each user-approved rule and links the full oracle in the spec while remaining within the existing Product Brief structure.
