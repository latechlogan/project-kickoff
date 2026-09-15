# project-kickoff

A Claude Code skill that plans a new project before any code exists: the brief and constraints, the stack, the data model, the architecture, the trust layer (tests, hooks, an independent reviewer agent), and delivery. It runs as a gated, resumable conversation and leaves the plan in the project's `docs/`, then hands off to the `workspace-scaffold` skill.

## Install

Clone this repo and symlink it into your Claude Code skills directory:

```sh
git clone git@github.com:latechlogan/project-kickoff.git
ln -s "$(pwd)/project-kickoff" ~/.claude/skills/project-kickoff
```

## Layout

| Path | Purpose |
| --- | --- |
| `SKILL.md` | The skill itself: principles, phases, and hand-off to the scaffold |
| `references/stack-familiarity.md` | The user's read/write familiarity by tool, consulted in the stack phase |
| `assets/*.md` | Templates the phases write into the project's `docs/` and `.claude/agents/` |
