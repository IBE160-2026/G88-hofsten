# Good-Spine Rubric Review

## Verdict

The spine is coherent at initiative altitude and fits the PRD's local demonstration scope, but it needs one high-severity enforcement clarification and two medium fixes before it is a dependable build contract. The deterministic linter passes. No material issue was found in the selected paradigm, the FR-1–FR-8 capability map, the Deferred boundaries, or the operational envelope.

## High

### AD-6 states AI restrictions without an enforceable boundary

**Spine sections:** `Invariants & Rules / AD-5 — Recommendation is a pure domain policy`; `Invariants & Rules / AD-6 — AI explains; it does not calculate or decide`; `Consistency Conventions / Verification`.

AD-6 says provider prose cannot introduce unsupported numeric claims or choose a different scenario, but it does not define a response contract or a check that can detect either violation. Free-form text can name a different scenario, misstate a displayed figure, or contradict the deterministic recommendation while still passing through the explanation coordinator. That conflicts with PRD FR-6 and FR-7's numeric-fidelity and recommendation requirements; testing provider success, missing-key, and failure paths alone does not establish those properties.

**Fix:** Make the application own the selected scenario and validate provider output before rendering. Specify a constrained response shape and post-validation for the selected scenario, source-backed numeric claims, and required explanation fields; reject invalid output and select the PRD-defined fallback. Add a provider-success test with contradictory scenario/numeric claims to verify rejection. Alternatively, keep all numeric and recommendation statements application-rendered and limit AI output to validated qualitative text.

## Medium

### Stack table conflates verified releases, project dependencies, and the actual repository state

**Spine sections:** `Stack`; `Structural Seed` (repository tree and `uv.lock` comment); `Consistency Conventions / Configuration and secrets`.

Python 3.14.10, Flask 3.1.3, and uv 0.13.9 are current releases as of the review date, but this repository has no `pyproject.toml`, `uv.lock`, or application package yet; the installed uv here is 0.13.8. In addition, uv is the dependency-management tool, not a dependency version managed by the project's lockfile. The current wording can be read as a verified, already-established stack rather than a proposed scaffold.

**Fix:** Label these versions as the proposed V1 baseline until the application scaffold exists. Separate the uv toolchain recommendation from runtime/dependency versions, and describe `uv.lock` as the future project dependency lockfile rather than implying it pins uv. Once scaffolded, verify the baseline against the checked-in project metadata and lockfile.

### PRD clarity and explanation-content obligations are not carried into the architecture contract

**Spine sections:** `Invariants & Rules / AD-6`; `Consistency Conventions`; `Capability → Architecture Map / FR-1, FR-6, FR-7`.

The map locates the dashboard and explanation coordinator, but no binding rule preserves several explicit PRD requirements: the interface must visibly identify the voyage data as simulated; the explanation must visibly distinguish live AI, predefined mock, and rule-based fallback; and explanations must cover both time and cost trade-offs and disclose the model's limitations. These are easy to implement inconsistently across the dashboard, live response, and fallback paths. PRD FR-1, FR-6, FR-7 and SM-3 make them testable acceptance requirements.

**Fix:** Add a concise presentation/explanation contract to the relevant consistency convention or AD: require visible text labels for simulated data and explanation source, and require both time/cost trade-offs and the applicable limitations in live and fallback explanations. Bind the rule to FR-1, FR-6, FR-7, and SM-3, and include these cases in the stated Flask rendering tests.

## Review Checks

- **Altitude and paradigm:** Initiative altitude matches the PRD scope; a ports-and-adapters modular monolith is coherent for one local Flask/Jinja prototype with deterministic domain calculations and an optional outbound AI adapter.
- **Enforceable ADs:** AD-1 through AD-5 and AD-7 specify useful structural or deterministic constraints. AD-6 is the material exception described above.
- **PRD coverage:** FR-1 through FR-8 are all present in the capability map, and SM-1 through SM-3 are bound in the spine metadata. The missing content obligations are detailed above.
- **Deferred decisions:** Provider/model/SDK and timeout/retry choices are appropriately deferred to implementing the optional adapter. Production hosting and persistent services are explicitly outside V1. No deferred item leaves a current V1 boundary ambiguous.
- **Current stack verification:** Official release sources confirm Python 3.14.10, Flask 3.1.3, and uv 0.13.9 as current on 2026-11-20. The project-state and lockfile wording still needs clarification as noted above.
- **Operational envelope:** Local development and demonstration, loopback Flask server, optional configured outbound AI, and absence of production hosting, telemetry, database, accounts, and live voyage integrations are explicit and consistent with the approved student-prototype scope.

## Verification Evidence

- Deterministic check: `uv run .claude/skills/bmad-architecture/scripts/lint_spine.py --workspace .docs/planning-artifacts/architecture/architecture-VoyageAI-2026-11-20` completed successfully.
- PRD checked: `.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/prd.md`, especially FR-1, FR-6–FR-7, SM-3, and the cross-cutting requirements.
- Version sources: [Python 3.14.10 release](https://www.python.org/downloads/release/python-31410/), [Flask on PyPI](https://pypi.org/project/Flask/), and [uv releases](https://github.com/astral-sh/uv/releases).