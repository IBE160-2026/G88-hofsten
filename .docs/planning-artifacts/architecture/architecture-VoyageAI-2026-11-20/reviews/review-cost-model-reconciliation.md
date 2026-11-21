# Cost-Model Reconciliation Review

## Verdict

Conditionally aligned. The spine preserves deterministic recomputation, unrounded comparisons and recommendation, the display-rounding boundary, simulated timezone/window endpoint conventions, and test seams. It does not contradict the approved calculation rules, but several important rules are only implied by “approved” references or omitted from the architecture contract, especially per-leg feasibility and berth-window transitions. Keep the current fixed-scenario scope; do not turn these gaps into a general speed-selection or optimization feature.

## Preserved

- **Determinism:** AD-3 recalculates from immutable source data and the current delay; AD-4 assigns calculations to pure domain functions; AD-5 uses unrounded ETA and cost for a deterministic recommendation; AD-6 bars AI from calculating or overriding results. This aligns with PRD FR-2 and FR-6 and the calculation-consistency requirement. See [spine AD-3–AD-6](../ARCHITECTURE-SPINE.md#L59), [PRD FR-2](../../../prds/prd-VoyageAI-2026-11-18/prd.md#L89), and [PRD FR-6](../../../prds/prd-VoyageAI-2026-11-18/prd.md#L166).
- **Precision boundary:** AD-4 and AD-7 retain unrounded values through comparison and presentation formatting, and specify half-up rounding only for displayed values. AD-5 explicitly compares unrounded ETA and cost. This is consistent with the PRD's half-up rules. See [spine AD-4, AD-5, and AD-7](../ARCHITECTURE-SPINE.md#L65) and [PRD FR-5](../../../prds/prd-VoyageAI-2026-11-18/prd.md#L128).
- **Clock and interval endpoints:** The spine specifies the timezone-naive simulated clock, inclusive delay range, and start-inclusive/end-exclusive berth intervals. See [spine conventions](../ARCHITECTURE-SPINE.md#L89) and [PRD FR-2/FR-3](../../../prds/prd-VoyageAI-2026-11-18/prd.md#L89).
- **Source delegation:** The spine cites both the PRD and implementation spec; the PRD identifies the spec as the calculation reference for the sample. The spine's concise reference to approved formulas does not conflict with or replace those rules. The 24-hour day, cubic fuel equation, USD 600/tonne price, waiting burn, and fuel/cost exclusions remain governed by those sources. See [spine sources and AD-4](../ARCHITECTURE-SPINE.md#L20) and [PRD FR-5](../../../prds/prd-VoyageAI-2026-11-18/prd.md#L128).
- **Testability:** Pure domain tests, Flask test-client coverage, an injected fake provider, and baseline/0-, 6-, and 12-hour oracles provide useful isolated and integrated checks. See [spine verification convention](../ARCHITECTURE-SPINE.md#L98).
- **Scope:** The spine retains three fixed responses, one approved sample, and no deployment/database/optimization expansion; behavior beyond the approved input/window envelope remains deferred. It does not prescribe a generic target-speed solver. See [spine AD-1](../ARCHITECTURE-SPINE.md#L39), [capability map](../ARCHITECTURE-SPINE.md#L143), and [Deferred](../ARCHITECTURE-SPINE.md#L152).

## Findings

### Feasibility is not an explicit architecture invariant

The spine assigns FR-4's three responses to the domain calculator, but neither AD-4 nor the capability map carries forward the inclusive 8–13-knot limit or the rule that an out-of-range target is infeasible, never clamped or represented as meeting a window. The PRD makes this a per-leg rule, and the spec gives the out-of-range 3 June target as a concrete example. The current three speeds are fixed sample values, not permission to build a general speed-selection policy.

**Reconciliation:** Add the per-leg feasibility/no-clamping rule to AD-4 or explicitly bind the calculator to PRD FR-5. Keep the three approved scenario speeds fixed; do not add target search or optimization. Sources: [spine AD-4 and FR-4 mapping](../ARCHITECTURE-SPINE.md#L65), [PRD FR-4/FR-5](../../../prds/prd-VoyageAI-2026-11-18/prd.md#L115), [spec calculation rules](../../../../implementation-artifacts/spec-voyageai-cost-model.md#L39).

### Window transitions are only partially stated

The convention table preserves start-inclusive/end-exclusive endpoints, and AD-4 refers generally to approved berth-window rules. The spine does not state the rest of the state transition: early arrival waits for opening; arrival at opening or during a window starts the 12-hour stay; arrival at or after closing waits for the next listed window; and the stay may finish after closing. These are normative calculation rules, not optional implementation details.

**Reconciliation:** State these transitions briefly in AD-4 or point there explicitly to PRD FR-5 and the cost-model specification as normative. Retain the current deferral for behavior after the final listed window; the approved input range does not reach that case. Sources: [spine AD-4 and time convention](../ARCHITECTURE-SPINE.md#L65), [PRD FR-5](../../../prds/prd-VoyageAI-2026-11-18/prd.md#L128), [spec calculation rules](../../../../implementation-artifacts/spec-voyageai-cost-model.md#L37).

### Exact scenario speed and display precision are not pinned in the spine

The spine names the three responses in its capability map but does not record their speeds. AD-4 protects unrounded values and half-up formatting, but does not itself prevent using display-only `9.09` knots instead of the exact `100/11`, nor state the display scales (ETA to the nearest minute, fuel to three decimals, cost to cents). The PRD and spec do state those rules.

**Reconciliation:** Bind FR-4/AD-4 explicitly to the exact fixed speeds and the PRD's display scales, or state that the cited PRD/spec values are normative and display approximations must never feed calculation. Do not select different speeds based on a delay. Sources: [spine AD-4 and FR-4 mapping](../ARCHITECTURE-SPINE.md#L65), [PRD FR-4/FR-5](../../../prds/prd-VoyageAI-2026-11-18/prd.md#L115), [spec precision and scenario rules](../../../../implementation-artifacts/spec-voyageai-cost-model.md#L37).

### Window tests do not explicitly name the just-before/at/just-after close cases

The verification convention calls for 0-, 6-, and 12-hour oracles. The 6-hour oracle exercises an arrival exactly at closing for the 10-knot scenario, but the spine does not explicitly require the adjacent 5:59 and 6:01 cases that distinguish the exclusive closing boundary from neighboring arrivals. Those expected waits are stated in PRD FR-3.

**Reconciliation:** Include the PRD's 5:59, 6:00, and 6:01 boundary assertions in the domain window tests. This strengthens verification of the already-approved interval rule without widening the input range or adding window behavior outside the fixture. Sources: [spine verification convention](../ARCHITECTURE-SPINE.md#L98), [PRD FR-3 boundary oracle](../../../prds/prd-VoyageAI-2026-11-18/prd.md#L112).

## Authority

The PRD frontmatter says `status: approved`, and its [.memlog.md](../../../prds/prd-VoyageAI-2026-11-18/.memlog.md) records the approval. Its §0 draft wording is stale, as identified by the spine's [Deferred note](../ARCHITECTURE-SPINE.md#L152); it is not a conflict in this reconciliation. The detailed fuel equations and exclusions are delegated to the cited cost-model specification.

## Review Basis

Compared the architecture spine with the implementation spec's calculation rules and hand-checked baseline, and PRD FR-3 through FR-5 plus SM-2. This report narrows the calculation-specific omissions also noted in the broader PRD reconciliation. No source or architecture edits were made. The PRD's 0-, 6-, and 12-hour result tables and its additional 6-hour cost oracle remain the intended fixtures; no generalized window or scenario-selection behavior is inferred.
