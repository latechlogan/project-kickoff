# Quality gates

What "clean code" means when a machine can check it, by ecosystem. Read in Phase 2 (pick the tools with the stack) and Phase 5 (put them in the check command with thresholds). The judgment half — names that say the right thing, functions that do one thing, comments that say why — lives in `assets/trust.md` `## Code quality` and goes to the reviewer. Nothing here duplicates it.

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

Verified against the tools' documentation on 2026-09-22. Re-check defaults when a major version lands.
