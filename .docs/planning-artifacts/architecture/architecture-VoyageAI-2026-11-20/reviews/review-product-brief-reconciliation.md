# Product Brief Reconciliation Review

## Verdict

**Needs reconciliation.** The spine preserves the prototype's local, non-production boundary, deterministic scenario calculation, optional AI, labelled 12-hour mock, and planner's final control. However, it changes who makes the scenario suggestion, narrows the sample-voyage boundary, and does not carry several constraints and user-facing caveats stated in the controlling product brief.

## Review Basis

The controlling source for this review is the repository-root `product-brief.md`, as requested. The PRD and implementation spec are not used to resolve differences. References below identify exact section headings in the brief and spine.

## Findings

### 1. Contradicted: AI suggestion is replaced by a fixed domain recommendation

**Brief:** `Proposed Solution`, step 5; `Role of AI` table; `Success Criteria`, item 3.

**Spine:** `AD-5 — Recommendation is a pure domain policy`; `AD-6 — AI explains; it does not calculate or decide`; `Capability → Architecture Map`, FR-6.

The brief assigns comparison, trade-offs, and the suggestion to the AI. The spine instead makes a pure domain policy select the recommendation and prohibits the AI from choosing a different scenario. The planner's final decision remains human, but the AI-versus-code responsibility boundary does not.

**Consequence:** The implemented product can visibly recommend an option without the AI doing the comparison and suggestion promised by the brief.

**Reconcile:** Keep scenario figures deterministic, but align the recommendation ownership with the brief. If deterministic selection is intentionally desired, update the controlling product requirement before treating AD-5 as authoritative.

### 2. Weakened: The recommendation objective is more prescriptive than the brief

**Brief:** `Proposed Solution`, step 5; `Role of AI` table.

**Spine:** `AD-5 — Recommendation is a pure domain policy`.

The spine introduces a specific eligibility rule (no later ETA at C than “Keep 10 knots”), then minimizes unrounded simulated fuel cost and breaks ties by ETA. The brief asks for an AI suggestion that explains which option looks most suitable on simulated figures and the time/cost trade-offs; it does not establish this lexicographic policy or make the “Keep 10 knots” ETA a decision threshold.

**Consequence:** The suggestion can optimize a policy the product brief never approved, and may not express the AI's promised assessment of the trade-offs.

**Reconcile:** Treat the policy as a separate product decision requiring explicit approval, or remove it from the spine and let the explanation follow the brief's AI responsibility.

### 3. Omitted: The AI must surface operational factors as checks, not assess them

**Brief:** `Proposed Solution`, step 5; `The Problem`; `Role of AI` table.

**Spine:** `AD-6 — AI explains; it does not calculate or decide`; `Capability → Architecture Map`, FR-6.

The brief requires the explanation to flag charter-party speed terms, laytime, and emissions as factors to check, and says the prototype does not calculate them. AD-6 constrains numbers and scenario selection but does not require these caveats or prevent the explanation from implying that they were assessed.

**Consequence:** Live and fallback explanations may omit material operational limitations or imply coverage the prototype does not provide.

**Reconcile:** Bind the explanation contract to identify these items as uncalculated checks, not conclusions, and preserve that behavior in the fallback.

### 4. Omitted: The provider's lack of contract data is not an explicit boundary

**Brief:** `Proposed Solution`, step 5; `Role of AI` table.

**Spine:** `AD-6 — AI explains; it does not calculate or decide`.

The brief explicitly says the AI has no contract data and therefore cannot assess contract factors. The spine limits the provider to calculated results and a recommendation, but does not state that contract data is unavailable or that contractual assessments are prohibited.

**Consequence:** A later adapter or prompt could invite or appear to provide a charter-party or laytime assessment unsupported by the product's inputs.

**Reconcile:** State that no contract data is supplied and that the AI must not assess contractual terms; direct it to flag them for human review.

### 5. Omitted: Cost exclusions are not carried into the architecture's disclosure contract

**Brief:** `Proposed Solution`, `Simulated cost model and sample voyage`; `Version 1 Scope`, in-scope cost model.

**Spine:** `AD-4 — Deterministic calculations with one precision boundary`; `Consistency Conventions`, `State and data` and `Errors and fallback` rows.

The brief explicitly excludes fuel during the departure delay and port stays, port fees, demurrage, and other commercial or contractual costs. AD-4 delegates formulas to an “approved cost-model” without preserving these exclusions or requiring them to be disclosed with the comparison.

**Consequence:** The UI or explanation can present a partial simulated fuel cost as if it represented total voyage or commercial cost.

**Reconcile:** Preserve the exclusions in the scenario-result/presentation contract and label the displayed cost as the narrow simulated model described in the brief.

### 6. Omitted: Simulated precision and rounding limitations are not stated

**Brief:** `Proposed Solution`, paragraph after the sample table; `Assumptions`, third and fourth bullets; `Success Criteria`, item 5.

**Spine:** `AD-4 — Deterministic calculations with one precision boundary`; `Consistency Conventions`, `Verification` row.

The spine correctly preserves unrounded values until presentation, but does not require the displayed rounded values to be identified as simulated and not real-world precision. Nor does it require assumptions and simplifications to be stated openly as the brief's honesty criterion requires.

**Consequence:** Precise-looking figures may be mistaken for real voyage-cost estimates or validated market data.

**Reconcile:** Keep the existing precision rule and also require presentation of the simulated basis and the brief's non-real-world-precision caveat.

### 7. Omitted: The unvalidated basis is not an explicit product invariant

**Brief:** `The Problem`; `Intended Users`; `Assumptions`; `Success Criteria`, item 5.

**Spine:** No corresponding invariant; closest references are `AD-3 — Recompute from the approved source voyage` and the local-demo paragraph after `Structural Seed`.

The brief says the problem and user assumptions have not been validated and that nothing should be presented as validated. The spine labels data as approved and constrains deployment, but does not preserve the unvalidated status or require it to be visible.

**Consequence:** “Approved” sample data or a polished dashboard can be read as evidence of validated user needs or operational performance.

**Reconcile:** Distinguish approval of a simulated test fixture from validation of the problem, assumptions, or product, and require that limitation to remain explicit.

### 8. Omitted: Per-leg speed feasibility and inclusive limits are not stated

**Brief:** `Proposed Solution`, `Simulated cost model and sample voyage`; `Version 1 Scope`, simulated speed limits.

**Spine:** `AD-4 — Deterministic calculations with one precision boundary`; `Consistency Conventions`, `Input and time` row.

The brief makes the 8–13-knot bounds inclusive per sailing leg and says out-of-range targets are infeasible, not clamped or treated as meeting a berth window. The spine delegates to approved formulas but does not state this boundary or its infeasibility behavior. AD-3's no-clamping instruction is for invalid delay input, not speed targets.

**Consequence:** An implementation can silently clamp or accept an infeasible speed and still appear consistent with the spine.

**Reconcile:** Add the inclusive per-leg range and explicit infeasible/no-clamping behavior to AD-4 or its input/model conventions.

### 9. Weakened: The product is narrowed to one fixed sample voyage

**Brief:** `Proposed Solution`, step 1; `Version 1 Scope`, in-scope voyage count and port calls.

**Spine:** `AD-3 — Recompute from the approved source voyage`; `Structural Seed`; `Capability → Architecture Map`, FR-1; `Deferred`, user-created voyages and additional samples.

The brief describes setting up a vessel, route, and sequence of port calls, says sample voyages are included, and scopes one voyage at a time with roughly 3–5 calls. The spine instead fixes the approved sample as the sole source voyage and defers both user-created voyages and additional samples. This is narrower than the brief; “one voyage at a time” does not itself mean “one hard-coded voyage only.”

**Consequence:** A user cannot perform the setup flow described in the brief, and the plural sample-voyage expectation is removed from V1.

**Reconcile:** Decide whether V1 supports selecting among sample voyages or only the fixed A–B–C fixture, then align the brief or spine's FR-1/AD-3 boundary accordingly.

### 10. Potential range mismatch: the spine rejects the brief's 36-hour example

**Brief:** `Proposed Solution`, step 2 gives “36 hours late departing port 2” as an example.

**Spine:** `Consistency Conventions`, `Input and time` row, limits delay to 0–720 minutes.

The spine's maximum is 12 hours, so the brief's 36-hour example cannot be entered. The brief introduces 36 hours with “for example,” so this is not conclusive evidence that 36 hours is a required V1 input; it is an unresolved user-facing mismatch.

**Consequence:** A user following the brief's stated example receives a validation error, with no explanation that the example is outside the supported envelope.

**Reconcile:** Either align the supported delay range with the example or identify the example as illustrative and disclose the narrower supported range.

## Requirements Preserved

- The spine keeps the application local and explicitly outside production operation; it excludes production hosting, live voyage data, and optimization.
- Deterministic calculations remain independent of AI availability, and the AI cannot introduce numeric claims absent from calculated results.
- The 12-hour baseline has a clearly labelled predefined mock when live AI is unavailable; scenario comparison remains available without a key. The rule-based fallback for other valid delays is an additional behavior, not a conflict with the brief.
- The planner remains the final decision-maker, and the spine excludes an operational-action adapter.
- The one-vessel/one-voyage-at-a-time and single-delay V1 limits are consistent with the brief, even though the fixed-single-sample interpretation is narrower.

## Open Reconciliation Decisions

1. Whether V1 includes multiple selectable sample voyages or only the fixed A–B–C fixture.
2. Whether the 0–12-hour input envelope is intentional despite the brief's 36-hour example.
3. Whether a deterministic recommendation policy is intended to supersede the brief's AI-owned suggestion, and, if so, whether the brief should be revised.