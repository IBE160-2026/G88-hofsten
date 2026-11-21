# Final Adversarial Review

## Verdict

**Clean: no residual real divergence holes found.** The configured adversarial pass attempted paired compliant implementations across the requested dimensions. For each pair, the current architecture contract either selects one behavior or defines a shared value boundary that prevents an incompatible interpretation. The earlier adversarial findings are addressed in the current spine.

The deterministic spine lint also passes with zero findings. The spine was not edited.

## Paired Implementation Attempts

| Dimension | Competing implementations attempted | Contract that prevents divergence |
| --- | --- | --- |
| Fixture ownership | Put the approved voyage fixture in `application` versus `domain`. | AD-2 names `domain` as sole owner; `application` may select and pass it but may not duplicate or redefine it. The state/data convention and structural seed agree. |
| Result ownership and shape | Pass separate scenario values versus a request-bound comparison; omit fields or interpret arrival/wait differently. | AD-7 makes domain the owner of immutable `ComparisonResult` and `ScenarioResult`; it binds the validated delay and exactly three ordered results, and names each field, unit, numeric type, and timestamp semantics. |
| Units and precision | Convert fuel to kilograms or cost to cents, use rounded display values in policy, or approximate `100/11` with a decimal. | AD-4 and AD-7 require the specified units, `Fraction` values through eligibility/comparison/recommendation, exact `100/11`, and rounding only at presentation. |
| Recommendation identity | Parse provider prose or a display label in one implementation; consume a policy-selected scenario ID in another. | AD-5 requires a typed `RecommendationResult` with the selected scenario ID, shared by live and fallback paths; rendering uses that result and never parses provider prose. AD-7 supplies stable scenario IDs and exact display labels in the shared comparison. Different private ID spellings do not alter identity because consumers use the ID carried by the domain-owned result rather than independently deriving it. |
| AI and fallback | Accept a successful but malformed, numeric, or contradictory provider response versus reject it; choose the mock by default/baseline history versus current delay. | AD-6 defines the qualitative-only `ExplanationDraft`, rejects nonconforming output, and maps unsuccessful output to fallback. The mock is selected iff the current valid delay is 720; other valid delays use rule-based fallback. Deadlines are finite and retries are disabled. |
| Request history | Add a new delay to previously shifted times or reuse earlier explanation text versus recompute from the approved original voyage and current request. | AD-3 prohibits persisted voyage/delay/planner-decision state and requires recomputation from the original schedule. AD-6 forbids reuse of earlier-delay text; AD-7 binds results to the current delay. Verification names the 720 → 360 → 0 → 720 repeated-edit sequence. |
| Labels and visible contract | Use interchangeable fallback labels, paraphrase the canonical mock, or rely on color to identify simulated data/source/decision authority. | AD-6 and the user-visible convention require exact PRD fallback labels, the mock verbatim, visible current comparison and trade-offs/limitations, simulated-data and planner-authority disclosures, and text labels rather than color alone. |
| Tests | Test only isolated calculations or provider success, leaving history, routing, and rendered behavior untested. | The verification convention specifies the calculation oracles and berth/speed boundaries, repeated edits, provider success and failure routes at 720 and a non-baseline delay, selected scenario, labels, comparison visibility, mock, trade-offs/limitations, current numeric facts, and rejection of numeric claims or an alternative recommendation. |

## Residual Findings

None. The freedom to choose private implementation details such as the concrete representation of the stable scenario ID is not a real divergence hole: the domain owns the shared contract and downstream code consumes the selected ID from that contract. No separate API or persisted identifier format is in scope.

## Verification

- `uv run .claude/skills/bmad-architecture/scripts/lint_spine.py --workspace .docs/planning-artifacts/architecture/architecture-VoyageAI-2026-11-20` — passed; 0 findings.
- Compared current AD-2 through AD-7 and consistency conventions with `reviews/review-adversarial-boundaries.md`; all prior adversarial gaps in the requested dimensions are closed.