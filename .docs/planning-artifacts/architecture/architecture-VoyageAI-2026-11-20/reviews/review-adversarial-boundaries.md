# Adversarial Boundary Review

## Verdict

The spine closes the calculation policy at a high level, but it is not yet a reliable handoff for independently implemented modules. AD-7 requires one shared immutable result contract without defining its fields or comparison envelope; sample-data ownership conflicts across the spine; and important live-provider and fallback boundaries remain underspecified. Two teams can satisfy the AD wording locally and still produce incompatible interfaces or behavior.

## Findings

### 1. Resolve who owns the approved sample

**Location:** AD-3; Consistency Conventions / State and data; Capability → Architecture Map / FR-1; Structural Seed.

**Trigger condition:** One unit follows “immutable application data” and puts the fixture under `application`; another follows the capability map and Structural Seed and puts it under `domain`. Both treat it as immutable source data and recompute from it, yet the dependency boundary and import direction disagree.

**Guard snippet:** Name `domain` as the sole owner of the immutable approved voyage fixture; `application` may select and pass it but must not duplicate or redefine it. Align the State and data convention and diagrams with that ownership.

**Potential consequence:** Duplicate fixture definitions can drift on scheduled times, distances, planned speeds, or windows while each team believes it implemented the approved sample.

### 2. Define the per-scenario result schema

**Location:** AD-7; Structural Seed / `domain/`.

**Trigger condition:** AD-7 says to share typed values but does not state the required fields or their semantics. One calculator can expose arrival ETA at B, berth wait, ETA at C, total fuel, and cost; another consumer may expect only ETA at C and cost, or may interpret an ETA as berth-service start rather than arrival.

**Guard snippet:** Publish one domain-owned `ScenarioResult` contract with stable field names and definitions for scenario identity, ETA at each modeled port, berth wait, fuel, and simulated cost. Distinguish arrival time from berth-window opening/service start where both are represented.

**Potential consequence:** Independently developed policy, explanation, and rendering modules cannot reliably consume each other's values, or omit a value required to explain the comparison.

### 3. Add a comparison-level result envelope

**Location:** AD-3 and AD-7; Structural Seed / Compare-voyage use case.

**Trigger condition:** The spine specifies individual scenario-result values but no object that binds all three scenarios to the accepted delay and one calculation. One use case may return a tuple of three results; another may pass scenarios independently without a shared delay or comparison identity.

**Guard snippet:** Define an immutable `ComparisonResult` containing the validated current delay and exactly the three scenario results calculated from that request's approved source voyage; pass the complete value through policy, explanation coordination, and rendering.

**Potential consequence:** A renderer or explanation path can combine scenario values from different submissions or fail to establish which accepted delay the displayed comparison represents.

### 4. Specify units and numeric types at the shared boundary

**Location:** AD-4 and AD-7; Consistency Conventions / Naming and modules.

**Trigger condition:** The PRD names units, but the spine's typed contract does not say how they are represented. One module can expose wait as integer minutes, fuel as tonnes, and cost as USD; another can expect a duration, kilograms, and integer cents. Explicit types alone do not make those values interchangeable.

**Guard snippet:** Define units and representations for every shared quantity, including timestamp/duration, fuel tonnes, and USD cost; keep display strings and display-rounded values out of the domain contract.

**Potential consequence:** Values can be misread or converted twice at module boundaries, producing incorrect policy comparisons or displayed results despite individually valid types.

### 5. Make the precision guarantee operational

**Location:** AD-4 and AD-7; Consistency Conventions / Verification.

**Trigger condition:** “Unrounded” does not choose an exact numeric representation or a comparison tolerance. One unit can retain exact rational values while another uses binary floats and treats their approximations as exact; the cost-model source permits exact fractions or unrounded values, but the spine does not state what precision the policy and formatter must preserve.

**Guard snippet:** Require exact arithmetic for rational sample speeds and derived times/costs, or specify an equivalent precision and boundary-comparison guarantee that reproduces every approved oracle and half-up display result. Apply rounding once, at presentation.

**Potential consequence:** Mathematically equal values may compare differently, or a value near a berth boundary or display-rounding midpoint can select a different wait, recommendation, or displayed amount.

### 6. Return the selected recommendation as data, not only prose

**Location:** AD-5 through AD-7; Capability → Architecture Map / FR-6 and FR-7.

**Trigger condition:** AD-5 defines which scenario wins but not the policy's output type or how the selected scenario reaches presentation. One coordinator can pass a selected `ScenarioResult`; another can pass a scenario identifier; a third can infer the recommendation by parsing the provider's explanation text.

**Guard snippet:** Define a policy result containing the canonical selected scenario identity, separate from explanatory text, and require live and fallback paths to render that same identity without parsing provider prose.

**Potential consequence:** The narrative can name a different response from the deterministic policy result, or the renderer can show no unambiguous selected scenario even though the policy selected one.

### 7. Define how a successful but nonconforming AI response is rejected

**Location:** AD-6; Structural Seed / Explanation coordinator and AI adapter.

**Trigger condition:** AD-6 prohibits unsupported numeric claims and alternate recommendations, but only names missing credentials and request failure as fallback triggers. A provider can return a successful HTTP response containing unsupported numbers, a different scenario, or unusable text; one adapter can trust it while another rejects it.

**Guard snippet:** Treat provider output as untrusted. Validate it against the current results and policy-selected scenario before display; if it violates the explanation contract or cannot be validated, discard it and use the same delay-selected fallback as for provider failure.

**Potential consequence:** A nominally successful AI call can still show fabricated numbers or contradict the domain recommendation, violating AD-6's central invariant.

### 8. Give the explanation port one failure outcome contract

**Location:** AD-2 and AD-6; Structural Seed / Explanation port and coordinator.

**Trigger condition:** The port is named but has no success/unavailable result semantics. One adapter may raise exceptions, another return `None` or an error string; coordinators can then classify the same outage differently or accidentally render an error as explanation text.

**Guard snippet:** Define a provider-neutral port outcome that distinguishes validated explanation from unavailable/nonconforming output. The coordinator alone maps every unsuccessful outcome to the specified fallback; provider-specific timeout and retry mechanics can remain deferred.

**Potential consequence:** Missing-key, outage, and malformed-response paths can diverge across adapters, leaving the comparison without the required fallback or displaying provider diagnostics to the planner.

### 9. Pin the mock-selection condition to the current delay

**Location:** AD-6; Consistency Conventions / Errors and fallback; Structural Seed / Fallback.

**Trigger condition:** “At the baseline” does not say whether the mock is selected whenever the current valid delay is 720 minutes or only for an initial/default baseline state. Two coordinators can route the same current 12-hour submission differently while treating “baseline” differently.

**Guard snippet:** For missing-key and unsuccessful-provider paths, select the predefined mock iff the validated current delay is exactly 720 minutes; use the rule-based fallback for every other valid delay from 0 through 719 minutes. Invalid input produces neither scenario results nor fallback.

**Potential consequence:** The same current comparison can receive different fallback modes depending on load history or interpretation of “baseline.”

### 10. Carry the rule-based fallback's full visible contract

**Location:** AD-6; Consistency Conventions / Verification.

**Trigger condition:** “Clearly labelled current-results-only” leaves open whether the exact PRD label is used, both time and cost trade-offs are explained, unsupported derived numbers are prohibited, and the current comparison remains visible with no previous-delay text. The PRD specifies these behaviors, but the architecture convention does not preserve them.

**Guard snippet:** Require the exact rule-based label, explicit notice that no live explanation was produced, comparison visibility, both time and cost trade-offs, no additional numeric calculations, and content generated only from the current request's scenario results. Require the 12-hour mock's approved text and label verbatim.

**Potential consequence:** Fallbacks can be distinguishable internally yet incomplete, stale, or misleading in the rendered experience.

### 11. Verify request-history and fallback boundaries explicitly

**Location:** Consistency Conventions / Verification.

**Trigger condition:** The convention lists calculation oracles and provider success/missing-key/failure paths, but does not require the repeated-edit sequence or the complete fallback route matrix and rendered assertions. Isolated delay tests can pass while request history or failure routing diverges.

**Guard snippet:** Add the 720 → 360 → 0 → 720 minute repeated-edit oracle, plus missing-key and failed-provider tests at 720 minutes and a non-baseline valid delay. Assert current comparison visibility, exact mode/label, recommendation identity, current-result numeric claims, and absence of stale explanation text.

**Potential consequence:** History-dependent mutation, incorrect fallback routing, or stale rendered text can ship despite the existing isolated calculation tests passing.

## Confirmed Alignment

- AD-5 closes the recommendation algorithm itself: Keep-10 ETA at C is the reference; eligibility uses unrounded ETA; selection minimizes unrounded simulated cost; equal-cost ties use earliest unrounded ETA. This matches the PRD rule, so no alternate selection algorithm is proposed here.
- AD-3 prohibits persistent voyage, delay, and planner-decision state, and requires recomputation from the original schedule. AD-7 also requires immutable result values. No additional persistent-state mechanism is needed; make the source owner and request-bound comparison explicit as above.
- AD-4 correctly places display rounding after calculation and recommendation. The finding is about making the underlying precision guarantee enforceable, not changing the approved half-up rules.

## Sources Checked

- `ARCHITECTURE-SPINE.md`
- PRD FR-1 through FR-7, especially FR-2, FR-5, FR-6, and FR-7
- `spec-voyageai-cost-model.md`
- `mock-12-hour.md`