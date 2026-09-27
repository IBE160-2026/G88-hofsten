---
title: "Product Brief: VoyageAI"
status: final
created: 2026-09-27
updated: 2026-09-27
---

# Product Brief: VoyageAI

## Summary

VoyageAI is a web-based prototype that supports voyage planning in shipping when a vessel is delayed at a port call. It shows how the delay affects the rest of the voyage, compares three standard responses, and uses AI to explain the trade-offs between them. The planner makes the decision.

VoyageAI is a solo student project in IBE160 Programmering med KI at Høgskolen i Molde. It is a small prototype built on simulated data. It is not a commercial voyage optimisation system. The project has two aims: to demonstrate AI-assisted and agentic software development, and to be relevant to the shipping domain.

This brief is the first-phase deliverable in the BMAD framework. It describes what VoyageAI is, why it is needed, who it is for, and how it is intended to work.

## The Problem

When a vessel is delayed at a port call, the planner is assumed to need a quick view of how the delay affects the rest of the voyage and which response fits best. This is hard because the effects spread and the factors pull against each other:

- **Schedule:** a later departure moves the ETA at every later port and may miss a berth window. The resulting wait can be longer than the original delay.
- **Cost:** domain literature describes fuel as typically the largest variable voyage cost, and fuel use rises steeply with speed. Recovering lost time is therefore expensive.
- **Contract:** delays affect laytime and possible demurrage, and changing speed can conflict with the charter party's speed and consumption terms.
- **Emissions:** speeding up increases emissions and can affect the vessel's CII rating and EU ETS cost.

This description is based on desk research and domain literature. It has **not been validated** with voyage planners.

## Proposed Solution

In VoyageAI the planner:

1. **Sets up a voyage:** a vessel, a route and a sequence of port calls with planned times. Sample voyages are included.
2. **Registers a delay** at one port call, for example "36 hours late departing port 2".
3. **Sees the impact** on the rest of the voyage: new ETAs, waiting time and change in cost.
4. **Compares three scenarios**, calculated side by side:
   - keep planned speed and accept the delay
   - speed up to recover lost time
   - slow down to arrive just in time for the next berth window
5. **Reads an AI explanation:** the AI summarises the differences, explains the trade-offs between time and cost, and describes operational considerations in words. It suggests which option looks most suitable on the simulated figures, and why. It also flags factors the prototype does not calculate as things to check, such as charter party speed terms, laytime and emissions. The AI has no contract data, so it cannot assess these factors.
6. **Decides:** the planner chooses. VoyageAI does not act on the decision.

## Role of AI

| Part | Handled by | Reason |
|---|---|---|
| ETAs, waiting time, cost figures | Deterministic code | The figures must follow correctly from the inputs and be repeatable |
| Comparison, trade-offs, suggestion | AI | Turns the scenarios into a plain-language explanation that helps the planner compare them |
| Final decision | The planner | Commercial and contractual judgement stays with a person |

The AI only uses figures calculated by the code and must not introduce numbers of its own.

## Intended Users

The intended users are people who work with voyage planning or maritime operations at a shipping company. Their needs as described in this brief are assumptions from domain literature and have not been confirmed with users.

## Version 1 Scope

**In scope:**

- One vessel and one voyage at a time, with roughly 3–5 port calls
- One type of disruption: a delay at one port call
- The three scenarios above, calculated by deterministic code
- A simplified, transparent cost model based on simulated data, clearly labelled as simulated
- An AI explanation and suggestion based on the calculated scenarios
- A web dashboard showing the voyage, the impact and the scenario comparison

**Out of scope:**

- Live AIS, real-time weather and port system integrations
- Route or speed optimisation
- Several vessels or voyages at the same time
- Other disruption types, and skipping or swapping ports
- Legally precise laytime or demurrage calculations
- User accounts and production operation

## Success Criteria

1. **Working demo:** using a sample voyage, a user can go from a delayed port call to three compared scenarios and an AI explanation in one flow on the dashboard.
2. **Consistent figures:** the scenario figures follow from the stated simulated inputs and formulas. A hand check on the sample voyages confirms this, and confirms that the AI explanation neither contradicts the figures nor adds numbers of its own.
3. **Clear division of roles:** the code calculates, the AI explains, and the user decides, and this is visible in the prototype.
4. **AI-assisted development:** the prototype is built using AI-assisted and agentic development, and this use is documented along the way.
5. **Honest basis:** assumptions and simplifications are stated openly, and nothing is presented as validated.
6. **Realistic for one person:** the core flow is delivered within the course timeline.

## Assumptions and Open Questions

**Assumptions:**

- A delay at a port call is a common and meaningful problem for voyage planners.
- Planners would find a side-by-side comparison with an explanation useful.
- A simplified cost model can illustrate the trade-offs clearly, although the simulated figures are not intended to represent actual commercial voyage costs.
- Speed and fuel relationships and port data can be simulated plausibly from public sources (see `addendum.md`).
- No external validation is planned, so the prototype demonstrates a concept, not proven user value.

**Open questions:**

- How do planners actually handle delays today?
- Which cost elements should the model include (for example fuel, waiting time, port costs)?
- Should operational risk get a simple calculated indicator, or stay as a description in the AI explanation?
