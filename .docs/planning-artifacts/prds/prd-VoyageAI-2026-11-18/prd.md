---
title: "PRD: VoyageAI"
status: approved
created: 2026-11-18
updated: 2026-11-19
---

# PRD: VoyageAI

## 0. Document Purpose

This draft defines the product behavior and V1 boundaries for VoyageAI, a web-based student prototype for comparing responses to a delay during a voyage. It is based primarily on the approved [Product Brief](../../../../product-brief.md); the [simulated cost-model specification](../../../implementation-artifacts/spec-voyageai-cost-model.md) is the calculation reference for the sample voyage. The existing Product Brief addendum remains unchanged and is background context, not a source for additional V1 features. This PRD is a draft for review; it is not approved or final.

## 1. Vision

VoyageAI helps a voyage planner understand how a delay at one port call may affect later arrivals and compare three fixed-speed responses on the A–B leg of the sample voyage: “Keep 10 knots,” “Increase to 12.5 knots,” and “Reduce to 100/11 knots on A–B.” It presents deterministic estimates alongside a plain-language AI explanation so the planner can weigh time and simulated fuel cost; the reduced-speed response does not promise a berth-window arrival except for the approved 12-hour baseline.

VoyageAI is a solo IBE160 student prototype intended for demonstration and academic assessment, not a commercial launch or operational pilot. Its voyage and cost data are simulated, and the described user need has not been validated through interviews. The planner remains responsible for the decision; VoyageAI does not act on it.

## 2. Target User

### 2.1 Jobs To Be Done

- When informed of a possible delay, a voyage planner wants to see its effect on the remaining port calls.
- The planner wants to compare the time and simulated fuel-cost consequences of three standard responses.
- The planner wants an explanation of the trade-offs while retaining responsibility for the final choice.

[ASSUMPTION: The target user and these needs are inferred from desk research and have not been validated with voyage planners.]

### 2.2 Non-Users (V1)

- Shipboard operators seeking a system that issues or executes operational instructions.
- Shipping companies seeking a production voyage-optimisation or commercial cost system.
- Users managing several vessels or voyages simultaneously.

### 2.3 Key User Journeys

- **UJ-1. Emma compares responses to a voyage delay.**
  - **Persona + context:** Emma is a hypothetical ships-operations planner who needs to assess a reported delay. This persona and journey are not based on interviews or observed use.
  - **Entry state:** Emma has information about a possible delay and opens the VoyageAI web prototype.
  - **Path:** She opens the single approved A–B–C sample voyage, enters or changes the departure delay at A, and views the recalculated arrival estimates and three response scenarios. She compares their arrival times and simulated fuel costs, then reads the live AI explanation or the clearly labelled fallback appropriate to the current delay.
  - **Climax:** Emma can see how the three responses compare on the displayed estimates and trade-offs.
  - **Resolution:** Emma chooses what she considers appropriate. VoyageAI records no operational instruction and makes no decision for her.
  - **Edge case:** If no live AI response is available, the prototype displays the approved mock for the 12-hour baseline or a clearly labelled rule-based explanation for another valid delay.

## 3. Glossary

- **Delay** — A reported lateness at one port call. V1 models one delay in a voyage.
- **Port call** — A planned visit to a port in the voyage sequence.
- **ETA** — Estimated time of arrival at a port call.
- **Berth window** — A listed time interval in which arrival can start the port stay under the sample cost-model rules.
- **Scenario** — One of the three fixed A–B speeds for the approved sample: “Keep 10 knots,” “Increase to 12.5 knots,” or “Reduce to 100/11 knots on A–B.” The reduced-speed scenario does not promise to meet a berth window except for the approved 12-hour baseline.
- **Simulated fuel cost** — The illustrative cost calculated from modelled sailing and berth-wait fuel at the simulated fuel price; it is not a real commercial cost.
- **AI explanation** — A plain-language comparison of calculated scenario results and their trade-offs, optionally including qualitative operational considerations.
- **Mock explanation** — The canonical, approved 12-hour baseline explanation in [mock-12-hour.md](mock-12-hour.md), shown instead of a live AI response when no API key is configured or an AI request fails.
- **Rule-based explanation** — A clearly labelled fallback when no live AI response is available for another valid delay, based only on current calculated scenario results and without adding new numerical results.

## 4. Features

### 4.1 Voyage and Delay

**Description:** The first development priority is a working demonstration using the single approved A–B–C sample voyage. The planner can enter or change the departure delay at A. Creating user-defined voyages and including additional sample voyages are excluded from V1 and recorded as possible future extensions in §6.2.

**Functional Requirements:**

#### FR-1: Open the approved sample voyage

The planner can open the approved A–B–C sample voyage to assess the consequences of a departure delay at A.

**Consequences (testable):**
- The sample presents the approved A–B and B–C legs, their planned speeds and the specified berth windows at B.
- The interface identifies the sample data as simulated and illustrative, not live operational data.

**Notes:** V1 includes the approved sample only; user-created voyages and additional samples are deferred future extensions, not V1 capabilities.

#### FR-2: Enter or change the sample departure delay

The planner can enter or change the delay to the approved sample voyage's departure from A, in whole minutes from 0 through 720 inclusive. Zero means no delay.

**Consequences (testable):**
- The current delay value and the affected departure at A are identifiable in the results.
- The system accepts integer-minute values from 0 to 720, including both endpoints.
- The system rejects an empty value, non-numeric or non-finite value, fractional-minute value, negative value, or value greater than 720 with a clear validation message. It does not clamp or silently adjust invalid input.
- No comparison or explanation is shown as if it matched an invalid value; results are refreshed only after a valid value is accepted.
- Changing the delay replaces the previous value; results are recalculated from the sample's scheduled times and current delay, not by adding the new value to previously shifted results.
- A sequence of valid edits (12 hours → 6 hours → 0 hours → 12 hours) produces the same complete result set for both occurrences of 12 hours.

### 4.2 Voyage Impact and Scenario Comparison

**Description:** VoyageAI shows how the current delay affects the remainder of the approved sample voyage and compares the three fixed-speed responses defined in FR-4. Calculated figures must follow the approved simulated cost model. The specification gives an exact hand-checked result for the baseline 12-hour delay.

**Functional Requirements:**

#### FR-3: Show downstream impact

For the approved sample and current delay, the planner can view the resulting ETA at each later port call and any modelled berth waiting time.

**Consequences (testable):**
- Results identify the affected sample port calls and their calculated ETAs.
- Where a berth window is modelled, results show the resulting waiting time.
- The approved A–B–C sample produces the ETAs and waiting times in FR-5.
- When the delay changes, displayed results correspond to the current value and are not stale results from the previous value.
- Berth-window start is inclusive and close is exclusive. An arrival exactly at closing waits for the next listed opening; arrival exactly at opening has no berth wait.
- With the approved windows, expected ETA at B and waiting time at the interval boundaries are:

| Delay | Keep 10 knots | Increase to 12.5 knots | Reduce to 100/11 knots on A–B |
|---|---|---|---|
| 0 hours | 3 June 12:00; 0 hours | 3 June 00:00; 6 hours | 3 June 18:00; 12 hours |
| 6 hours | 3 June 18:00; 12 hours | 3 June 06:00; 0 hours | 4 June 00:00; 6 hours |
| 12 hours | 4 June 00:00; 6 hours | 3 June 12:00; 0 hours | 4 June 06:00; 0 hours |

- At 5 hours 59 minutes, 6 hours, and 6 hours 1 minute, the 10-knot scenario arrives at B at 3 June 17:59, 18:00, and 18:01 respectively; waiting is 0, 12 hours, and 11 hours 59 minutes.
- Every valid delay from 0 to 12 hours yields a defined wait for all three scenarios using the two approved windows. No arrival reaches or passes the closing time of the second window within this input range.

#### FR-4: Compare the three responses

The planner can compare side by side the three fixed-speed responses for the current sample delay: “Keep 10 knots,” “Increase to 12.5 knots,” and “Reduce to 100/11 knots on A–B.” B–C remains at 10 knots in all three scenarios.

**Consequences (testable):**
- All three scenario results are presented together for the same sample voyage and current delay.
- Each result identifies its response and displays the resulting ETA and simulated fuel cost.
- The reduced-speed scenario is labelled “Reduce to 100/11 knots on A–B” and does not promise to arrive at a berth window for delays other than the approved 12-hour baseline.
- The baseline 12-hour delay reproduces the approved values in FR-5.
- No additional user-defined scenario or optimization feature is implied by these three standard responses.

**Notes:** The fixed speeds are approved only for the A–B leg of this sample. The approved cost model and its baseline 12-hour results remain unchanged.

#### FR-5: Calculate the approved sample cost model

For the approved sample voyage, VoyageAI calculates sailing and berth-wait fuel using the rules in the cost-model specification and the simulated fuel price of USD 600 per tonne.

**Consequences (testable):**
- Daily sailing fuel at speed `v` knots is `12 × (v / 10)^3` tonnes per day; partial days are prorated over 24 hours.
- Berth-wait fuel is 1 tonne per day. Fuel during the departure delay and port stays is excluded.
- The sample berth windows at B are 3 June and 4 June, 06:00–18:00. They are start-inclusive and end-exclusive. An early arrival waits for opening; arrival during an open window starts the 12-hour stay; arrival at or after closing waits for the next listed window. The stay may finish after closing.
- The inclusive 8–13-knot limit applies per sailing leg. A target outside the limit is infeasible; it is not clamped or reported as meeting the window.
- Port fees, demurrage, and other commercial or contractual costs are excluded.
- Calculations and berth-window eligibility use unrounded elapsed times. Displayed ETAs are rounded half-up to the nearest minute; displayed fuel is rounded half-up to three decimals and cost half-up to cents. The exact `100/11` speed is used in calculations; `9.09` is display-only.
- All timestamps use one timezone-naive simulated clock; year and timezone conversion are not modelled.
- For the approved A–B–C sample (12-hour departure delay at A; A–B 600 nautical miles; B–C 300 nautical miles; 10-knot planned speed), the comparison is:

| Response | A–B speed | ETA B | Berth wait at B | ETA C | Total modelled fuel | Simulated fuel cost |
|---|---:|---|---:|---|---:|---:|
| Keep 10 knots | 10 knots | 4 June 00:00 | 6 hours | 6 June 00:00 | 45.250 tonnes | USD 27,150.00 |
| Increase to 12.5 knots | 12.5 knots | 3 June 12:00 | 0 hours | 5 June 06:00 | 61.875 tonnes | USD 37,125.00 |
| Reduce to 100/11 knots on A–B | 100/11 knots (about 9.09) | 4 June 06:00 | 0 hours | 6 June 00:00 | 39.793 tonnes | USD 23,876.03 |

**Notes:** These values and rules are the approved sample fixture, not a general scenario-speed selection policy. All dates and values are simulated; the cost model is not a real-world estimate.

**Additional hand-checked case — 6-hour delay at A:** This is an additional PRD test oracle derived from the approved cost-model formulas, not a change to the approved 12-hour fixture. Departure from A is 1 June 06:00. At 10 knots, ETA B is exactly 3 June 18:00; because closing is exclusive, the vessel waits 12 hours for the 4 June 06:00 window. At 12.5 knots, ETA B is exactly 3 June 06:00, the inclusive opening, so waiting is zero. At 100/11 knots, ETA B is 4 June 00:00 and waits 6 hours for the next window.

| Response | ETA B | Wait at B | A–B fuel | Wait fuel | B–C fuel | Total fuel | Simulated cost | ETA C |
|---|---|---:|---:|---:|---:|---:|---:|---|
| Keep 10 knots | 3 June 18:00 | 12 hours | 30.000 t | 0.500 t | 15.000 t | 45.500 t | USD 27,300.00 | 6 June 00:00 |
| Increase to 12.5 knots | 3 June 06:00 | 0 hours | 46.875 t | 0.000 t | 15.000 t | 61.875 t | USD 37,125.00 | 5 June 00:00 |
| Reduce to 100/11 knots on A–B | 4 June 00:00 | 6 hours | 24.793 t | 0.250 t | 15.000 t | 40.043 t | USD 24,026.03 | 6 June 00:00 |

Fuel check: A–B fuel is respectively `12 × 60/24 = 30`, `12 × (12.5/10)^3 × 48/24 = 46.875`, and `12 × (10/11)^3 × 66/24 = 33000/1331 ≈ 24.793388` tonnes. B–C fuel is `12 × 30/24 = 15` tonnes in all scenarios. Waiting fuel is `wait hours/24` tonnes. The 100/11-knot total is `33000/1331 + 1/4 + 15 = 213191/5324 ≈ 40.043388` tonnes; cost is calculated from unrounded fuel and rounded half-up to USD 24,026.03. These calculations exclude the departure delay and port-stay fuel as specified by the approved model.

### 4.3 AI Explanation and Human Decision

**Description:** When available, AI explains scenario results already calculated by the product. If no live AI response is available, the product uses the approved mock or the rule-based fallback defined in FR-7. Explanations may describe qualitative considerations such as charter-party speed terms, laytime, and emissions as matters to check; the prototype has no contract data to assess them. No calculated risk indicator or score is part of V1.

**Functional Requirements:**

#### FR-6: Recommend and explain a scenario

When a live AI response is available, the AI recommends which of the three scenarios appears most suitable based on the simulated results and explains why. This is decision support, not an operational decision or instruction.

**Consequences (testable):**
- Use the current-delay `Keep 10 knots` scenario's unrounded ETA at C as the reference.
- A scenario is eligible for recommendation only if its unrounded ETA at C is the same as or earlier than the reference ETA.
- Recommend the eligible scenario with the lowest unrounded simulated fuel cost. Compare costs before display rounding.
- If eligible scenarios have equal unrounded cost, recommend the one with the earliest unrounded ETA at C.
- The explanation names one of the three scenarios and states which displayed ETA, berth-wait, and simulated fuel-cost results support the suggestion and what trade-off those results show.
- The explanation always covers both arrival-time and simulated-fuel-cost trade-offs, including when a more expensive eligible scenario arrives earlier.
- The explanation states relevant limitations: the figures are simulated, and charter-party terms, laytime/demurrage, emissions, and other unmodelled considerations are not assessed because the prototype has no such data.
- The explanation presents the suggestion as decision support; the planner remains responsible for choosing an option.
- The AI does not introduce numerical values that were not calculated by the product.
- Qualitative operational considerations are presented as factors to check, not as assessed or calculated risks.

#### FR-7: Explain the current comparison when no live AI response is available

When no live AI response is available, whether because the API key is absent or the AI request fails, VoyageAI shows the canonical, approved [12-hour mock explanation](mock-12-hour.md) for the baseline delay and a clearly labelled rule-based explanation for other valid delays in the approved range.

**Consequences (testable):**
- Scenario calculations and comparison remain available when no live AI response is available.
- If an AI request fails despite a configured API key, the scenario comparison remains visible and the fallback is selected by delay: the predefined mock at 12 hours and the rule-based explanation at other valid delays.
- At 12 hours, the system displays the exact canonical text in [mock-12-hour.md](mock-12-hour.md), including the label “Predefined mock — not a live AI explanation”; it does not generate, rewrite, or omit its copy during fallback.
- The canonical mock recommends the scenario selected by FR-6's reference-ETA and unrounded-cost rule, and every numeric claim matches the approved 12-hour scenario results in FR-5; no contradictory or additional numeric claim is allowed.
- At every other valid delay, including a failed AI request with a configured key, the system selects a clearly labelled “Rule-based fallback — not a live AI explanation.”
- The rule-based fallback applies the same recommendation rule as FR-6: use the current `Keep 10 knots` ETA at C as reference, consider scenarios with unrounded ETA at C no later than that reference, select the lowest unrounded simulated fuel cost, and break equal-cost ties by earliest unrounded ETA at C.
- The rule-based fallback uses only the current delay and current calculated scenario ETA, berth-wait, fuel, and cost results. Each displayed number must match a value in those scenario results; no extra calculation, percentage, saving, risk score, or unsupported numeric claim is introduced.
- The rule-based fallback always explains both time and cost trade-offs and identifies if an eligible, more expensive scenario arrives earlier.
- In both fallback cases, the scenario comparison remains visible, the user is told no live AI explanation was produced, and no fallback content from a previous delay is shown.
- Tests cover missing-key and simulated request-failure paths at 12 hours and at one non-baseline valid delay; both verify label, fallback selection, scenario visibility, and numeric claims.

#### FR-8: Keep the planner in control

VoyageAI supports comparison but does not execute, transmit, or make the planner's operational decision.

**Consequences (testable):**
- The user remains responsible for selecting an option.
- The product does not take action on the user's behalf as a consequence of the comparison or AI explanation.

### 4.4 Cross-Cutting Requirements

- **Simulated-data clarity:** The interface and explanations distinguish illustrative simulated values from live operational data and real commercial costs.
- **Calculation consistency:** The same sample inputs produce the same ETA, waiting-time, fuel, and cost results, and displayed rounding does not alter the underlying comparison.
- **Explanation boundary:** Scenario figures come from deterministic calculations; AI and rule-based explanations describe those results but do not calculate or invent additional numbers.

## 5. Non-Goals (Explicit)

- Commercial or operationally authoritative voyage planning, cost estimation, or optimization.
- Automated decision-making or execution of voyage changes.
- Calculated operational-risk indicators or scores.
- Live AIS, weather, port-system, or other real-time integrations.
- Several vessels or voyages at once.
- User-created voyage setup in V1; this remains a possible future extension of the original product idea.
- Additional sample voyages in V1; the approved A–B–C voyage is the sole V1 sample, with more samples a possible future extension.
- Disruptions other than one delay at one port call; skipping or swapping ports.
- Legally precise laytime, demurrage, contract, emissions, or port-cost calculations.
- Treating qualitative considerations as assessed from contract or operational data that the prototype does not have.
- User accounts or production operation.

## 6. MVP Scope

### 6.1 In Scope

- The approved A–B–C sample voyage, with an editable departure delay at A.
- The three standard responses and a side-by-side comparison of downstream ETAs, berth waiting where modelled, and simulated fuel cost for the current delay.
- The approved transparent fuel-cost model and hand-checked A–B–C sample fixture.
- Simulated 8–13-knot per-leg bounds.
- An AI explanation based only on calculated figures; when no live AI response is available, the approved mock explanation at 12 hours and a clearly labelled rule-based explanation at other valid delays.
- A web dashboard for the voyage impact and scenario comparison.
- Qualitative operational factors as explanation context only, without risk scoring.

### 6.2 Out of Scope for MVP

- User-created voyage setup and additional sample voyages. These are deliberate V1 scope reductions from the original product idea, deferred for feasibility, testability, and the project's course timeline; they remain possible future extensions.
- Additional disruption types and the other items listed in Non-Goals.
- External user validation; the prototype demonstrates a concept and does not claim proven user value.

### 6.3 Project Constraints

- The first development stage is limited to one student working within the IBE160 course timeline. This is a feasibility constraint on the project, not a product success metric.

## 7. Success Metrics

**Primary**
- **SM-1: Interactive sample flow and recommendation.** A user can open the single A–B–C sample, enter or change the departure delay at A, and see all three scenario results and the applicable explanation update for the current value. At representative delays of 0, 6, and 12 hours, both the live AI recommendation and the rule-based fallback (when used) match the eligibility, unrounded-cost, and tie-break rules in FR-6. When no live AI response is available, at 12 hours the predefined mock is shown; at other valid values a clearly labelled rule-based explanation is shown. Validates FR-1–FR-7.
- **SM-2: Correct and repeatable calculations.** A hand check confirms the unchanged 12-hour baseline in FR-5 and the complete 6-hour ETA, waiting, fuel, and cost oracle in FR-5. The 0–12-hour boundary cases in FR-3 and repeated edits produce results from original inputs, without cumulative shifts. Validates FR-2–FR-5.

**Secondary**
- **SM-3: Clear decision boundary.** Pass if a reviewer can identify from visible text labels (1) scenario figures as simulated calculations, (2) whether the explanation is live AI, predefined mock, or rule-based fallback, and (3) that the planner makes the final decision. These distinctions must not depend on colour alone. Validates FR-6–FR-8.

**Counter-metrics (do not optimize)**
- **SM-C1: No false precision or invented figures.** Do not optimize the apparent certainty or persuasive force of an explanation at the expense of simulated-data labeling, numeric fidelity, or the planner's authority. Counterbalances SM-1 and SM-3.

**Project-process criterion (not a product metric)**
- **SP-1: AI-assisted development is documented.** Project records document AI-assisted and agentic development throughout the work, in line with [Product Brief Success Criterion 4](../../../../product-brief.md#L101). This criterion concerns the development process, not VoyageAI's user-facing features.

## 8. Open Questions

1. How do voyage planners handle delays today? This need remains unvalidated; no external user validation is claimed for V1.
2. If the delay range or window data is expanded, what should happen for an arrival at or after the final listed window? The current 0–12-hour input range does not reach that undefined case.

## 9. Assumptions Index

- **A-1, §2.1 Target User:** The voyage planner's needs are inferred from desk research and have not been validated with users.
