# PRD Quality Review — VoyageAI

## Overall verdict
The PRD is strong for its declared scope: a solo IBE160 demonstration using one simulated A–B–C voyage, deterministic scenario calculations, and bounded AI decision support. All seven findings from the previous Validate are resolved, and the canonical baseline and new six-hour oracle are consistent with the approved model; one low-severity scenario-label inconsistency remains. The draft is not represented as validated user need or an operational product, and its two open questions are explicitly outside current V1 behavior.

## Decision-readiness — adequate
The consequential V1 decisions are explicit: the approved sample is the sole V1 voyage, A–B speeds are fixed, qualitative operational considerations are not scored, and the planner retains authority. The scope reduction from the Product Brief is given a reason (feasibility, testability, and course timeline) in §§4.1, 5, and 6.2. The two §8 questions remain genuinely open: the underlying planner workflow is unvalidated, and behavior after the last listed berth window matters only if the delay range or windows are expanded. Neither is silently treated as a current capability or an implementation requirement.

### Findings
No findings.

## Substance over theater — strong
The single journey is tied to the sample comparison and explicitly hypothetical (§2.2); the target need is labeled as an assumption (§2.1). The PRD does not claim market validation, commercial accuracy, or novel operational optimization. It excludes risk scoring and explains that charter-party, laytime, demurrage, and emissions considerations are not assessed (§§1, 4.3, 5). The success metrics and counter-metric reinforce the narrow demonstration thesis rather than dressing it up as a production product.

### Findings
No findings.

## Strategic coherence — adequate
The thesis, feature sequence, and scope cohere: show downstream delay impact, compare three fixed responses on time and simulated fuel cost, then explain the trade-off while leaving the decision with the planner. FR-6 gives the live explanation a defined recommendation rule; SM-1 and SM-2 check the flow and calculations, while SM-3 and SM-C1 protect the decision boundary and numerical fidelity (§7). Evidence of user value remains unvalidated, but that limitation is visible and external validation is expressly out of scope for this prototype.

### Findings
No findings.

## Done-ness clarity — adequate
FR-1–FR-5 define a testable sample contract, including 0–720 whole-minute validation, replacement rather than cumulative edits, berth-window boundary semantics, fixed responses, calculation inputs, exclusions, precision, and expected outputs. FR-6 identifies the reference ETA, eligibility condition, unrounded cost comparison, tie-break, required explanation content, and limitations. FR-7 differentiates the exact 12-hour mock from the rule-based fallback for other valid delays and specifies missing-key and request-failure paths, labels, current-result sourcing, numeric fidelity, visibility, and test coverage. FR-8 makes the human decision boundary observable. These requirements give downstream implementation and test planning a usable definition of done; no material behavior is left to adjectives such as “reasonable” or “user-friendly.”

### Findings
No findings.

## Scope honesty — strong
The PRD explicitly excludes user-created voyages and additional samples from V1 while retaining them as possible future extensions (§§4.1, 5, 6.2). It also excludes live integrations, other disruptions, multi-voyage operation, commercial cost coverage, user accounts, and operational execution. The sample-only reduction is called out as a deliberate change from the original Product Brief, with rationale rather than silent omission. The unvalidated user need and the final-window edge case are visible, and neither is presented as solved.

### Findings
No findings.

## Downstream usability — adequate
The glossary defines the key domain terms; FR-1–FR-8, UJ-1, SM-1–SM-3, SM-C1, and SP-1 are unique and contiguous within their respective sequences; and referenced FRs resolve. UJ-1 has a named protagonist and carries the hypothetical/unvalidated context inline. The local Product Brief, cost-model, and mock links resolve, including the link from SP-1 to the Product Brief's AI-assisted-development criterion. The 12-hour fixture and derived six-hour oracle are extractable for implementation and test planning. One low-impact response-label drift is worth normalizing before generating stories or exact-label tests.

### Findings
- **low** Baseline scenario labels drift from the canonical response names (§4.2 FR-5) — The 12-hour table labels the first two responses “Keep planned speed” and “Speed up,” while FR-4, the six-hour oracle, and the canonical mock use “Keep 10 knots” and “Increase to 12.5 knots.” The figures are consistent, but aliases can create avoidable ambiguity in UI labels and exact-copy tests. *Fix:* use FR-4's response names consistently in both FR-5 tables and downstream fixtures.

## Shape fit — strong
The capability-and-journey shape is proportionate to a solo academic prototype with a meaningful comparison workflow. One short journey is enough to ground the interaction; detailed calculation requirements earn their space because those figures are the demonstration's deterministic oracle. The document explicitly disclaims commercial launch and operational-pilot use and does not import production-grade integrations or multi-user requirements.

### Findings
No findings.

## Mechanical notes
- **Previous Validate findings:** All four medium findings are resolved: FR-7 now links the approved exact-copy mock and defines fallback checks; FR-6 requires a recommendation and rationale under an explicit rule; SP-1 carries the IBE160 AI-assisted/agentic development objective as a project-process criterion; and FR-5 includes a separate hand-checked six-hour oracle. All three low findings are resolved: `updated` is 2026-11-19; SM-3 has visible-label/reviewer-identification pass conditions; and FR-5 specifies half-up rounding, ETA rounding, and a timezone-naive simulated clock.
- **Canonical mock:** FR-7 links `mock-12-hour.md`, requires the exact canonical block and fallback label, and says not to rewrite or omit its copy. Its recommendation and numeric claims agree with FR-5's 12-hour results: the reduced-speed scenario ties the 10-knot ETA at C, costs USD 23,876.03 versus USD 27,150.00, and the 12.5-knot scenario arrives 18 hours earlier at USD 37,125.00.
- **Recommendation identity:** FR-6 and the FR-7 rule-based fallback both use Keep 10 knots ETA at C as reference, admit scenarios with unrounded ETA no later than that reference, select the lowest unrounded simulated cost, and break a cost tie by earlier unrounded ETA at C. The live explanation must name the option and explain time and cost trade-offs; no alternative algorithm is implied.
- **Six-hour oracle and specification boundary:** The 6-hour table is explicitly a PRD-derived hand-check, not a change to the frozen cost-model spec, whose approved hand-check is the 12-hour baseline. Recalculation confirms the reduced-speed A–B fuel is 33000/1331 = 24.793388430 tonnes; adding 0.25 tonnes waiting fuel and 15 tonnes B–C fuel gives 40.043388430 tonnes and USD 24,026.03 after half-up cent rounding. The other rows also follow the approved duration, window, fuel, and stay rules; no contradiction with the 12-hour fixture was found.
- **IBE160 process objective:** SP-1 is explicitly marked as a project-process criterion, not a user-facing metric, and its Product Brief Success Criterion 4 link resolves to line 101.
- **Final V1 scope:** One approved A–B–C sample only; no user-created voyage or additional sample; fixed A–B responses; one delay at A; no external user validation, live operational data, risk score, or execution. This matches the latest non-superseded memlog decisions. The older memlog questions on scope, sample count, and failure fallback are superseded, not stale PRD questions.
- **Open questions:** OQ-1 accurately records that planner needs remain unvalidated; it is a research limitation, not a V1 delivery blocker. OQ-2 is conditional on expanding the input range or berth windows, so the undefined post-final-window case does not contradict the present 0–720-minute range.
- **References, IDs, metrics, and assumptions:** The Product Brief, cost-model spec, mock, and Product Brief line anchor resolve. FR-1–FR-8, UJ-1, SM-1–SM-3, SM-C1, and SP-1 have no duplicate or unresolved cross-reference. The single inline `[ASSUMPTION]` in §2.1 round-trips to A-1 in §9; no `[NOTE FOR PM]` callouts are present. SM-1–SM-3 have observable checks and corresponding FR references; SM-C1 is explicitly a counter-metric.
- **Terminology:** The only notable label drift is the §4.2 FR-5 baseline row wording identified above; formulas, scenario identity, and values remain consistent.