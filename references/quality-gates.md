# Quality gates

What "clean code" means when a machine can check it, by ecosystem — plus the supply-chain rows, the rows a UI switches on, and what is deferred. Read in Phase 2 (pick the tools with the stack) and Phase 5 (put them in the check command with thresholds). The judgment half — names that say the right thing, functions that do one thing, comments that say why — lives in `assets/trust.md` `## Code quality` and goes to the reviewer. Nothing here duplicates it.

Why this file exists: agents write more code than the job needs, reuse less of what already exists, and almost never tidy on their own. In one 2026 study of 1.38M agent commits, readability-motivated commits were 0.3% of the total, and even those raised complexity in 43% of cases. PR-level studies find more clones and more dead code than in human PRs. A paragraph asking for clean code is the weakest lever available; a gate that fails is the strongest.

Rules for using it:

- Every row the ecosystem can check goes in the check command. If it isn't in the check command it isn't enforced.
- **Thresholds are tripwires, not targets.** Start at the tool's default or the number here, log it as a decision, and move it after the first slices land. A threshold set too tight on day one teaches everyone to ignore it — the same rule Phase 5 applies to mutation score.
- One tool per property. When two see the same thing (tsc's `noUnusedLocals` and eslint's `no-unused-vars`) pick one; the typescript-eslint maintainers recommend the lint rule because it can ignore `_`-prefixed names and catch unused catch bindings.
- Layering rules are written from Phase 4's placement table: one forbidden rule per arrow the table forbids.
- Baselines are for brownfield. A greenfield project starts at zero clones and zero unused exports and stays there.
- A row with no tool in this ecosystem is written down in `docs/trust.md`, because the reviewer carries it.

## JavaScript / TypeScript

| Property | Tool | Rule or flag | Start at | Notes |
|---|---|---|---|---|
| Unused locals, params, imports, catch bindings | eslint via typescript-eslint | `@typescript-eslint/no-unused-vars` with `argsIgnorePattern: "^_"` | error | Leave tsc's `noUnusedLocals` / `noUnusedParameters` off — one tool |
| Unused exports, files, dependencies, types, enum and class members | knip | `knip`; add `--production` (or `--strict`) once entry points are marked with `!` | error — exits 1 on findings | Sees the whole module graph, which eslint cannot. `--fix` removes unused exports and dependencies |
| Orphan modules, circular imports | dependency-cruiser | forbidden rules with `from: { orphan: true }` and `to: { circular: true }` | error | Overlaps knip on orphans; keep depcruise for cycles and layering, knip for the rest |
| Layering, dependency direction | dependency-cruiser | one `forbidden` rule per arrow: `from: { path: "^src/core" }, to: { path: "^src/adapters" }` | error | Globals that need no import (`fetch`, `Date`, `process`) are banned per directory with eslint `no-restricted-globals` |
| Function complexity | eslint core, eslint-plugin-sonarjs | `complexity` (cyclomatic; default 20), `sonarjs/cognitive-complexity` (default 15) | `complexity: 10`, cognitive 15 | Cognitive complexity charges for nesting, so it tracks "hard to read" better than cyclomatic does |
| Function and file size, nesting, parameter count | eslint core | `max-lines-per-function`, `max-depth`, `max-params`, `max-lines` | 50 lines (skip blanks and comments), depth 3, 4 params, 300 lines | `max-params` pushes toward an options object, which is what you want |
| Duplication | jscpd; `sonarjs/no-identical-functions` | `jscpd src --threshold 0`; brownfield: `--fail-on-new-clones` against a baseline | 0% new | Type-2 and type-3 clones; 224 formats including Astro, Vue, Svelte |
| Name shape (case, prefixes) | eslint via typescript-eslint | `@typescript-eslint/naming-convention` | camelCase / PascalCase / UPPER_CASE by kind | Syntax only. Whether the name says the right thing is judgment |
| Commented-out code | none reliable | — | — | Judgment; the reviewer greps the diff for `^\s*//\s*(const|let|if|return|import|export)` |
| Formatting | prettier | `prettier --check .` | — | The PostToolUse hook runs `--write` on the touched file |

## Python

| Property | Tool | Rule or flag | Start at | Notes |
|---|---|---|---|---|
| Unused imports, variables | ruff | `F401`, `F841` | error | |
| Unused arguments | ruff | `ARG` | error | |
| Commented-out code | ruff | `ERA001` | error | Python gets this mechanically; JS does not |
| Unused functions, classes, unreachable code | vulture | `vulture src --min-confidence 80` | error | Whole program; ruff's `F` rules are per file |
| Complexity and size | ruff | `C901` with `max-complexity = 10`; `PLR0912` branches, `PLR0913` arguments, `PLR0915` statements | as listed | |
| Layering | import-linter | one `layers` or `forbidden` contract per arrow | error | |
| Duplication | jscpd | as above | 0% new | |
| Name shape | ruff | `N` (pep8-naming) | error | |
| Formatting | ruff | `ruff format --check` | — | |

## Other ecosystems

Fill the same rows. Every mainstream ecosystem has a tool for unused code, complexity, and duplication (Go: `staticcheck`, `gocyclo`, `dupl`; Rust: rustc's dead-code lints and `clippy::cognitive_complexity`). Say in `docs/trust.md` which rows have no tool.

## Supply chain

Agents add packages where a person would write ten lines, and sometimes name packages that don't exist but sound right; attackers register those names. Everything here fires on a signal, not a calendar.

| Property | Tool | Rule or flag | Fires when | Notes |
|---|---|---|---|---|
| Known advisories, in session | `pnpm audit` / `npm audit` / `pip-audit` | `--audit-level=high` in the check command | An advisory lands on a dependency, transitive included | The Stop hook has the agent bump the package; the user decides only when no patched version exists. `high` keeps low and moderate findings from blocking unrelated work |
| Known advisories, between sessions | Dependabot security updates | Repo Settings → Code security, one toggle | Same advisory, caught when nobody is working | Opens one PR per advisory and nothing else. The agent cannot switch it on; the hand-off names it as the user's |
| A new dependency is what it claims | the reviewer | `npm view <name> time.created`, weekly downloads, exact name | A dependency is added, which is already ask-first | Published in the last few weeks with few users is a must-fix; no stated reason is a should-fix |

## When a UI is built

Switched on by Phase 4's "built in v1" decision, regardless of who uses it: the mechanical half costs a config file and a few seconds. Phase 1's "who" switches on the judgment half.

| Property | Tool | Rule or flag | Runs in | Notes |
|---|---|---|---|---|
| Accessibility, at source | `eslint-plugin-jsx-a11y` (React); `eslint-plugin-astro` `flat/jsx-a11y-recommended` (Astro) | recommended config, error | check command; PostToolUse feedback on save | Catches the agent classics before the file is saved: `<div onClick>`, an icon button with no name, an input with no label |
| Accessibility, built pages | `pa11y-ci` or `@axe-core/cli`; `@axe-core/playwright` inside an end-to-end test | WCAG 2.1 AA, zero violations | Check command for a static build (file paths into `dist/`); the end-to-end layer from `/vet` when it needs a running server | Axe's own count is roughly half of real issues by volume. Colour contrast on a chosen palette is the one finding the agent should not fix alone |
| Bundle size | `size-limit` | one limit per entry in `package.json`; start at current size + 10% | check command | Agents add dependencies; a ceiling makes it visible. Raising the limit is a logged decision |

Judgment half, when anyone but the user uses it: a keyboard-only walk of the main flow at each milestone, and the reviewer's question — does every new interactive element have a name, a role, and a visible focus state.

## Deferred, and why

Each fires on a calendar or needs tuning, so it fails the "runs quietly and flags only when it needs attention" test. Log the deferral; revisit when the stated condition arrives.

| Tool | What it would add | Why it waits | Revisit when |
|---|---|---|---|
| Renovate | Version freshness: a PR per upstream release | A stream of PRs across every repo whether or not anything is wrong. The version that meets the bar — monthly, grouped, automerge on minor and patch when the check command passes, majors as PRs — needs CI on every PR, which spends Actions minutes on private repos | A repo has users, or a major falls more than one version behind |
| License checker | Fails on a disallowed licence | Matters for a company shipping a product; not for public solo repos | Anyone but the user redistributes the code |
| Lighthouse CI | Performance and best-practice scores | Scores flicker run to run, thresholds need tuning, needs a running server | Hosting runs it for free, or a UI has users on slow connections |

Verified against the tools' documentation on 2026-09-22. Re-check defaults when a major version lands.
