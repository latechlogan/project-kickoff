# {{PROJECT_NAME}} — Trust layer

<!-- Written by project-kickoff, Phase 5. Read when writing tests, adding a check, or deciding whether green means done. /vet reads this and dispatches the reviewer. -->

## What green means here

Passing checks mean the code does what the acceptance criteria say and nothing mechanical is broken. They do not mean the right thing was built. The user reviews at milestones — against the sequence diagrams in `docs/architecture.md`, the acceptance criteria on each unit of work, and the slice boundaries — not line by line.

## Acceptance criteria

Every {{UNIT_OF_WORK}} carries a `## Done looks like` section written as checkable statements, approved by the user before implementation starts. Tests are named after the criteria they prove ({{NAMING_CONVENTION_EXAMPLE}}). A test that doesn't trace to a criterion is either a regression guard for a logged learning or a candidate for deletion.

## Test layers

| Layer | Exists | Protects against | Runs in |
|---|---|---|---|
| Unit | {{YES_NO}} | {{FAILURE_MODE}} | check command |
| Integration | {{YES_NO}} | {{FAILURE_MODE}} | check command |
| End to end | {{YES_NO}} | {{FAILURE_MODE}} | {{CHECK_OR_VET_OR_CI_ONLY}} |

Tests run against {{FIXTURES_FROM_DATA_MODEL}}; see `docs/data-model.md`.

## The check command

`{{CHECK_COMMAND}}` runs, in order: format check, lint, typecheck, tests, secrets scan ({{SCANNER}}). Everything that gates work calls this and nothing else — hooks, pre-push, CI. If a check isn't in here, it isn't enforced.

{{IF_SLOW}}: the full suite is slow, so the Stop hook runs `{{FAST_SUBSET_COMMAND}}` (format, lint, typecheck) and `/vet` and pre-push run the full check.

## Mechanical vs judgment

**Mechanical (hooks, at scaffold time):**

- PostToolUse on Edit|Write: `{{FORMATTER}}`
- Stop: `{{FAST_CHECK_OR_FULL_CHECK}} || exit 2`

**Judgment (REVIEW.md):**

- {{QUESTION_A_REVIEWER_ASKS}}
- {{QUESTION_A_REVIEWER_ASKS}}

## Code quality

Same split. A check that isn't in `{{CHECK_COMMAND}}` isn't enforced; a question that isn't in REVIEW.md isn't asked. There is no style guide: the language's conventions are known, the rows below catch the rest.

**Mechanical (in the check command):**

| Property | Tool | Threshold |
|---|---|---|
| Unused locals, params, imports | {{LINT_RULE}} | error |
| Unused exports, files, dependencies | {{KNIP_OR_VULTURE}} | error |
| Function size and complexity | {{RULES}} | {{THRESHOLDS — e.g. complexity 10, cognitive 15, 50 lines, depth 3, 4 params}} |
| Duplication | {{JSCPD}} | {{ZERO_NEW_OR_BASELINE}} |
| Layering — the arrows in `docs/architecture.md` | {{DEPCRUISE_OR_IMPORT_LINTER}} | error |
| Name shape | {{NAMING_RULE}} | error |

{{ROWS_WITH_NO_TOOL — "Every row has a tool" or "No tool for X in this ecosystem; the reviewer carries it"}}

Thresholds are tripwires, not targets: these are starting values, logged as decisions, moved once the first slices land.

**Judgment (REVIEW.md `## Readability`) — asked by a reader who was not in the session:**

- Do names use the module map's words? Could you say what a value holds without reading the implementation?
- Does each function do one thing, at one level of abstraction, and read top to bottom?
- Do comments say *why*? A comment that narrates the code, or commented-out code, is a finding.
- Is anything speculative — an abstraction with one caller, an option nobody asked for, handling for a case that cannot happen?
- Did the diff re-implement something the repo already had?
- Does every new file sit in the row `docs/architecture.md`'s placement table gives it, and follow the pattern of its neighbours?

## Mutation testing

{{ENABLED_OR_SKIPPED}} — {{REASON}}. {{IF_ENABLED: Tool: `{{STRYKER_OR_MUTMUT}}`; scope: `{{CORE_PATHS}}`; threshold: {{PERCENT}}; runs via `{{COMMAND}}` from /vet, not the Stop hook.}}

## The reviewer

`.claude/agents/reviewer.md` is an independent agent with its own context. It receives the unit of work and the diff, reads this file and REVIEW.md, runs the check command, and reports must fix / should fix / fine. It never reads the conversation that produced the code — that separation is the point.

## For /vet

When the scaffold writes this project's `/vet`, its body should:

1. Run any script-only checks first ({{NONE_OR_LIST}}).
2. Dispatch the `reviewer` subagent with: the current {{UNIT_OF_WORK}} file, `git diff {{BASE}}`, and paths to `docs/trust.md` and `REVIEW.md`.
3. Return the reviewer's report unchanged; do not soften it.
