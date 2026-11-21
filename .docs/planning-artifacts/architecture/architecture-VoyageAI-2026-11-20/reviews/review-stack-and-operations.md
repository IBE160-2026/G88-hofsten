# Stack and Operations Review

## Verdict

**Versions verified; stack fit is good for the stated local demonstration.** Python 3.14.10, Flask 3.1.3, and uv 0.13.9 were released by 2026-11-20, and the official compatibility guidance found no blocker for this combination. The spine appropriately excludes production hosting, so a production WSGI server is not a missing V1 dependency. The operational boundary is clear, but the development setup is not yet executable from the spine: runtime/tool pins, local secret loading, and bounded provider calls need a concrete contract. The fallback's broad selection rule is sound, but its user-visible details and change-delay verification should be carried through explicitly.

## Scope and Method

This review evaluates the named stack and runtime, deployment, local-secret, and AI-fallback decisions in [ARCHITECTURE-SPINE.md](../ARCHITECTURE-SPINE.md), as of 2026-11-20. It checks official release and compatibility sources and compares operational claims with the approved [PRD](../../../prds/prd-VoyageAI-2026-11-18/prd.md). No implementation manifest or lockfile is present in the workspace, so release versions can be verified but not confirmed as resolved project dependencies.

## Version and Fit Check

| Entry | Verification as of 2026-11-20 | Fit |
| --- | --- | --- |
| Python 3.14.10 | Confirmed on the official [Python release page](https://www.python.org/downloads/release/python-31410/), released 2026-10-25 as a maintenance and expedited security release. | Suitable. Flask supports Python 3.9 and newer; uv lists CPython 3.14 as Tier 1. |
| Flask 3.1.3 | Confirmed in the official [Flask changelog](https://flask.palletsprojects.com/en/stable/changes/) and [Pallets release](https://github.com/pallets/flask/releases/tag/3.1.3); the changelog dates it 2026-02-18 and records a session-access security fix. | Suitable for a server-rendered Flask/Jinja prototype on Python 3.14. Flask already depends on Jinja; it need not be separately selected as another framework. |
| uv 0.13.9 | Confirmed in the official [uv release](https://github.com/astral-sh/uv/releases/tag/0.13.9), released 2026-11-17. | Suitable as a project/development tool. uv supports Python 3.14 at Tier 1 and manages project environments and lockfiles. It is not an application runtime dependency and `uv.lock` does not pin the uv executable itself. |

No version incompatibility or unnecessary runtime dependency is evident in the named stack. Keeping the provider SDK/model undecided until the optional adapter is implemented is reasonable; select only the provider integration actually needed. The Python floor `>=3.14` is not the same thing as selecting the 3.14.10 interpreter, and the exact uv tool version should be documented separately from locked application packages.

## Assessment

The ports-and-adapters modular monolith, pure calculation/policy modules, Flask/Jinja presentation, and optional outbound AI adapter fit the fixed-sample, deterministic V1. Avoiding a frontend service, database, worker, queue, and production hosting is consistent with the stated scope. The spine explicitly limits operation to a local development/demo server on loopback and says it is not a production target. That is an adequate deployment decision for this V1; do not add a production server or hosting stack unless the scope changes.

The current fallback split also makes sense: when live AI is absent or fails, use the canonical 12-hour mock at the baseline and a rule-based explanation for other valid delays while retaining deterministic scenario results. The remaining issues are about making those choices reproducible and ensuring each failure reaches the correct, visibly labeled fallback.

## Findings

1. **The stack is not yet evidenced by project metadata.** The workspace has no `pyproject.toml`, `uv.lock`, or `.python-version`. Thus the table records intended versions, but there is no declared Python requirement, Flask dependency pin, resolved dependency graph, or interpreter pin to validate. Add the project metadata and lockfile when scaffolding, and verify them with a locked sync/check; keep the exact local interpreter selection explicit.

2. **The Stack row conflates uv with lockfile-managed dependencies.** uv is the package-management executable that reads/writes `uv.lock`; its own version is not a dependency version stored there. Separate application/runtime requirements (Python and Flask) from developer tooling (uv), and state how the uv version is installed or pinned so contributors do not assume the lockfile fixes it.

3. **The Python support range and named interpreter are different contracts.** `baseline: >=3.14` accepts later Python minor versions, while `3.14.10` names one exact patch interpreter. Define which versions are supported/tested versus which version is the reproducible local baseline; add CI coverage or a deliberate upper bound if later minor versions are not yet supported.

4. **No update policy accompanies exact patch versions.** Both Python 3.14.10 and Flask 3.1.3 include security fixes current on the review date, but exact pins can become stale. State who refreshes the interpreter and lockfile, how security releases are prioritized, and what check demonstrates that the updated lock remains consistent. This is a maintenance gap, not evidence that the selected versions are stale today.

5. **The local runtime boundary lacks a runnable recipe.** The spine says to run Flask on loopback but does not name the application entry point/factory, command, port, or debug/reloader policy. Add those once the package scaffold exists, explicitly binding to `127.0.0.1` and keeping the debugger off unless deliberately needed. The present local-only/no-production boundary is otherwise clear.

6. **“Local environment configuration” does not say how credentials reach the process.** Name the provider-key environment variable when a provider is selected, and choose either shell-provided environment variables or documented `.env` loading. Flask's `.env` loading requires its optional `python-dotenv` dependency; it is not guaranteed by Flask itself. If `.env` is chosen, add a non-secret example and ensure the real file is ignored. Do not add `python-dotenv` if shell environment configuration is the intended path.

7. **The Flask session-signing secret is not distinguished from the AI key.** Flask requires `SECRET_KEY` if the app uses signed sessions (including features built on them), while the spine only discusses the optional provider credential. State whether V1 uses Flask sessions; if so, require a separate local, non-committed secret and never use a public development default. If sessions are not used, document that assumption and test the app without session access.

8. **A stalled provider call may prevent fallback indefinitely.** The provider timeout is deferred, yet AD-6 promises fallback when a request fails. A connection that never completes may not raise an error in a useful time, so the comparison page can remain blocked rather than showing the fallback. Require finite connect/read or overall deadlines and bounded retries (or none), and test the timeout path independently from calculation failures.

9. **Fallback presentation requirements are thinner than the PRD contract.** AD-6 states which fallback type to select, but does not require the exact “Predefined mock — not a live AI explanation” and “Rule-based fallback — not a live AI explanation” labels, the canonical 12-hour copy, notice that no live explanation was produced, comparison visibility, or numeric-claim fidelity. Add these as application/presentation acceptance and test assertions. The PRD already defines them; this is a trace-through gap, not a conflicting fallback policy.

10. **The changed-delay fallback path needs an explicit stale-content test.** AD-3 and AD-6 correctly require recomputation from the current input and current results, but the verification convention does not assert that fallback text from an earlier delay is absent after the delay changes. Test missing-key and provider-failure behavior at 12 hours and a non-baseline delay, then change the delay again and verify the rendered explanation, label, and numbers all correspond to that request only.

## Document Structure and Prose

Purpose/audience read: this document is a build substrate for implementers who need to preserve the V1 architecture and its behavioral boundaries. The closest structure model is an architecture/reference hybrid: concise invariants are useful for lookup, with operational instructions grouped together.

| Pass | Original Text | Revised Text | Changes |
| --- | --- | --- | --- |
| structure | Runtime paragraph following the Structural Seed diagram | MOVE it under a short “Local Runtime and Operations” heading, with the startup recipe when known | The paragraph defines runtime/deployment policy rather than structure; moving it makes the decision easy to find without adding scope. |
| structure | Stack table | Separate runtime versions from development tooling | Preserves the compact table while making clear that Flask/Python are app runtime choices and uv is a project tool. |
| prose | “uv 0.13.9 (lockfile-managed project dependencies)” | “uv 0.13.9 (project and dependency manager; `uv.lock` records resolved project packages)” | Removes the implication that the lockfile pins uv itself. |
| prose | “Read credentials from local environment configuration” | “Read the provider credential from a named environment variable; document whether developers set it in the shell or load it from an ignored `.env` file.” | Makes the setup path testable and distinguishes optional dotenv support from environment variables provided by the shell. |

These are density/clarity improvements; no broad restructuring is needed. The existing diagrams and requirement-to-architecture map serve the implementation audience and should be retained.

## Sources

- [Python 3.14.10 release](https://www.python.org/downloads/release/python-31410/)
- [Python 3.14 “What’s New” and release notes](https://docs.python.org/3.14/whatsnew/3.14.html)
- [Flask 3.1 installation and Python support](https://flask.palletsprojects.com/en/stable/installation/)
- [Flask 3.1 changelog](https://flask.palletsprojects.com/en/stable/changes/)
- [Flask deployment guidance](https://flask.palletsprojects.com/en/stable/deploying/)
- [Flask production tutorial: secret key and development-server boundary](https://flask.palletsprojects.com/en/stable/tutorial/deploy/)
- [uv 0.13.9 release](https://github.com/astral-sh/uv/releases/tag/0.13.9)
- [uv Python support policy](https://docs.astral.sh/uv/reference/policies/python/)
- [uv lock and sync behavior](https://docs.astral.sh/uv/concepts/projects/sync/)

## Findings Data

```json
[
  {
    "location": "Stack and Structural Seed",
    "trigger_condition": "No pyproject.toml, uv.lock, or .python-version is present to substantiate the selected versions.",
    "guard_snippet": "Add project metadata, a resolved lockfile, and an explicit interpreter pin; validate with a locked sync.",
    "potential_consequence": "The stated stack cannot yet be reproduced or audited as an actual project environment."
  },
  {
    "location": "Stack table",
    "trigger_condition": "uv is described beside lockfile-managed dependencies, although uv.lock does not pin the uv executable.",
    "guard_snippet": "Separate runtime dependencies from developer tools and document how uv itself is pinned or installed.",
    "potential_consequence": "Contributors may assume the lockfile reproduces the package-manager version."
  },
  {
    "location": "Stack table; Consistency Conventions",
    "trigger_condition": "The open-ended Python floor and exact 3.14.10 baseline do not define the same support contract.",
    "guard_snippet": "State supported/tested Python minors separately from the exact local interpreter baseline.",
    "potential_consequence": "A later Python minor may be treated as supported without project validation."
  },
  {
    "location": "Stack table",
    "trigger_condition": "Exact patch versions have no stated security-update or lock-refresh cadence.",
    "guard_snippet": "Document an owner and process for reviewing Python and dependency security releases.",
    "potential_consequence": "A reproducible pin may remain in use after upstream security fixes."
  },
  {
    "location": "Structural Seed; paragraph after diagram",
    "trigger_condition": "Local startup, entry point, port, and debug/reloader settings are not specified.",
    "guard_snippet": "Document the app command and bind to 127.0.0.1 with the debugger disabled by default.",
    "potential_consequence": "Developers may not launch the demo consistently or may expose the dev server unintentionally."
  },
  {
    "location": "Consistency Conventions — Configuration and secrets",
    "trigger_condition": "The credential variable and shell-versus-dotenv loading mechanism are unspecified.",
    "guard_snippet": "Name the provider variable and document shell configuration or ignored .env loading; add python-dotenv only if needed.",
    "potential_consequence": "A configured key may not reach the process, or a developer may commit a real secret."
  },
  {
    "location": "Consistency Conventions — Configuration and secrets",
    "trigger_condition": "The optional provider key is not distinguished from Flask's session-signing SECRET_KEY.",
    "guard_snippet": "State whether V1 uses sessions; if it does, configure a separate local secret and prohibit a public default.",
    "potential_consequence": "Session-backed features can fail or use a predictable signing key."
  },
  {
    "location": "Deferred — AI provider timeout and retry details; AD-6",
    "trigger_condition": "A provider request that hangs has no deadline to reach the promised fallback.",
    "guard_snippet": "Set finite connect/read deadlines and bounded retries, then test timeout fallback separately.",
    "potential_consequence": "A slow upstream can block the comparison instead of returning deterministic results."
  },
  {
    "location": "AD-6; Consistency Conventions — Verification",
    "trigger_condition": "Fallback labels, exact mock copy, live-unavailable notice, visibility, and numeric fidelity are not explicit spine checks.",
    "guard_snippet": "Assert the PRD's exact labels/copy, current comparison visibility, and result-only numeric claims.",
    "potential_consequence": "The fallback route can be selected correctly but presented ambiguously or inaccurately."
  },
  {
    "location": "AD-3; Consistency Conventions — Verification",
    "trigger_condition": "No explicit test proves a changed delay cannot retain fallback text from a prior request.",
    "guard_snippet": "Test missing-key and request-failure fallbacks at 12 hours and another delay, then change delay and verify all output is current.",
    "potential_consequence": "The planner can see a stale explanation or stale numeric claims after editing the delay."
  }
]
```