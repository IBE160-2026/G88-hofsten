# Mock Reconciliation Review

## Verdict

Partially reconciled. The recommendation policy and numerical boundary agree with PRD FR-6/FR-7 and the approved 12-hour mock. The architecture spine does not yet capture the exact mock-selection gate, required fallback labels and copy, the full rule-based fallback contract, or the test matrix needed to preserve them.

## Findings

### 1. State the exact mock-selection boundary

**Location:** `ARCHITECTURE-SPINE.md`, AD-6 and Consistency Conventions / Input and time (lines 81, 94–96)

AD-6 says to use the mock “at the baseline” and the rule-based explanation for “other valid delays,” but does not explicitly bind the mock to the single value `delay_minutes == 720`. FR-7 requires the mock only at 12 hours and the rule-based fallback at every other valid delay, for both a missing key and a failed request.

**Required clarification:** For either no-key or request-failure routing, select the predefined mock only when the valid delay is exactly 720 minutes; select the rule-based fallback for valid delays 0–719; reject values outside the inclusive 0–720 range without scenario or fallback results.

**Risk:** Implementations may treat “baseline” as a default sample or route adjacent delays to the mock instead of the rule-based explanation.

### 2. Make the canonical mock label and copy immutable requirements

**Location:** `ARCHITECTURE-SPINE.md`, AD-6 and Consistency Conventions / Verification (lines 81, 98)

“Canonical 12-hour mock” and “canonical mock copy” do not explicitly require the exact approved block, its label, or prohibit rewriting/omitting text. FR-7 requires the exact text from `mock-12-hour.md`, including “Predefined mock — not a live AI explanation,” without generating or rewriting it.

**Required clarification:** Render the approved block verbatim at 720 minutes, including its label and limitations, and verify its numeric claims against the approved FR-5 baseline. Do not have the live provider generate, paraphrase, or augment this fallback.

**Risk:** A renderer or provider adapter could alter required wording or omit the visible distinction between predefined mock and live AI.

### 3. Specify the rule-based fallback’s complete response contract

**Location:** `ARCHITECTURE-SPINE.md`, AD-6 (line 81)

“Clearly labelled current-results-only” does not enumerate the PRD’s required rule-based label or guardrails. FR-7 also requires no unsupported derived numbers, coverage of both time and cost trade-offs, identification of an eligible but more expensive earlier-arriving scenario, notice that no live AI explanation was produced, and no content carried over from a previous delay.

**Required clarification:** Require the label “Rule-based fallback — not a live AI explanation”; use only values from the current calculated scenario results; prohibit additional calculations and unsupported numerical claims; explain both trade-offs and any eligible earlier-but-more-expensive option; and regenerate from the current delay so stale fallback text is never shown.

**Risk:** A fallback can be labelled yet still omit required comparisons, introduce unsupported claims, or display text for a prior input.

### 4. Lock fallback routing and presentation in the verification convention

**Location:** `ARCHITECTURE-SPINE.md`, Consistency Conventions / Verification (line 98)

The convention calls for missing-key and provider-failure paths and the 0-, 6-, and 12-hour calculation oracles, but does not explicitly require checking fallback selection and its visible contract across both provider states and baseline/non-baseline delays. FR-7 requires missing-key and simulated-failure coverage at 12 hours and at a non-baseline valid delay, including label, selected fallback, scenario visibility, and numeric claims.

**Required clarification:** Assert the route matrix for missing key and simulated request failure at 720 minutes and at a non-baseline valid delay. Verify exact mock copy/label at 720, the rule-based label and current-result claims otherwise, visible scenario comparison, and no stale fallback content.

**Risk:** Calculation oracles can pass while fallback routing, labels, or rendered results violate FR-7.

## Confirmed Alignment

- **Numerical input boundary:** The spine specifies an integer delay from 0 through 720 minutes, inclusive, matching the approved range.
- **FR-6 recommendation:** AD-5 uses the current `Keep 10 knots` ETA at C as reference, includes equal-or-earlier unrounded ETAs, minimizes unrounded simulated fuel cost, and breaks equal-cost ties by earliest unrounded ETA at C. This is the PRD rule and correctly keeps recommendation ownership in the domain rather than the provider.
- **Canonical baseline choice and figures:** The mock recommends “Reduce to 100/11 knots on A–B.” Its 6 June 00:00 ETA at C equals the Keep-10 reference and its USD 23,876.03 cost is lower than Keep 10’s USD 27,150.00; the faster scenario’s 5 June 06:00 ETA and USD 37,125.00 cost also match FR-5. The mock’s numeric claims are consistent with the approved baseline.
- **Planner ownership:** The spine prevents the AI from changing the policy-selected scenario and leaves the final decision with the planner, consistent with FR-6/FR-8 and the mock.

## Sources Checked

- `.docs/planning-artifacts/architecture/architecture-VoyageAI-2026-11-20/ARCHITECTURE-SPINE.md`
- `.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/mock-12-hour.md`
- `.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/prd.md`, FR-6 and FR-7