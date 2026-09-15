# {{PROJECT_NAME}} — Brief

<!-- Written by project-kickoff. Sections marked (Phase N) are filled in that phase; delete any section that doesn't apply rather than leaving it empty. -->

## What

{{ONE_SENTENCE}}

## Who

{{WHO_USES_IT_AND_HOW}}

## Done looks like

{{V1_AS_OBSERVABLE_BEHAVIOR}}

**Walking skeleton (milestone 1):** {{THINNEST_END_TO_END_PATH}} — tested, running on {{RUNTIME_TARGET}}.

## Explicitly out of scope

- {{NOT_THIS}}
- {{NOT_THAT}}

## Constraints

| Constraint | Value | Design response |
|---|---|---|
| Runs on | {{RUNTIME}} | |
| Hosted at | {{HOSTING_OR_NONE}} | |
| Budget | {{BUDGET}} | |
| Repo visibility | {{PUBLIC_OR_PRIVATE}} | {{CI_MINUTES_RESPONSE}} |
| Secrets | {{WHAT_THEY_ARE_AND_WHETHER_CI_HAS_THEM}} | |
| External APIs | {{NAME_AND_QUOTA}} | |
| Deadline | {{DATE_OR_NONE}} | |

**Must never happen:**

- {{NEVER_THIS}}

## Stakeholders

<!-- Delete if the user owns this outright. -->

{{WHO_APPROVES_WHAT}}

## Stack (Phase 2)

| Layer | Choice | Decided by |
|---|---|---|
| Language / runtime | {{CHOICE}} | {{FIT_OR_CONSTRAINT_OR_FAMILIARITY}} |
| Framework | {{CHOICE}} | |
| Persistence | {{CHOICE_OR_NONE}} | |
| Tests | {{CHOICE}} | |
| Format / lint / types | {{CHOICE}} | |
| Hosting | {{CHOICE_OR_NONE}} | |

**Alternative considered:** {{ALTERNATIVE_AND_WHY_NOT}}

**Dependency policy:** lockfile committed; versions pinned; adding a dependency beyond {{THE_BAR}} is a logged decision, not a default.

## Delivery (Phase 6)

- **Deploy path:** {{PUSH_TO_MAIN_OR_TAG_OR_MANUAL}}
- **Rollback:** {{HOW}}
- **CI runs:** the check command (`{{CHECK_COMMAND}}`), triggered on {{PRS_OR_MAIN_OR_ALL}}
- **CI limit and response:** {{LIMIT_AND_DESIGN}}
- **Pre-push:** runs the same check command
- **Config:** validated at startup; `.env.example` committed; secrets in {{WHERE}}; CI has {{WHICH_SECRETS}}
- **Failure modes:** {{EXTERNAL_DEP}} — {{RETRY_TIMEOUT_IDEMPOTENCY}}

## Decisions

<!-- One per line, scaffold grammar. Lifted verbatim into DECISIONS.md at hand-off, then this section is deleted. -->

- {{YYYY-MM-DD}} — [decision] {{ENTRY}}

## Kickoff status

- [ ] 1 Brief
- [ ] 2 Stack
- [ ] 3 Data and state
- [ ] 4 Architecture and interfaces
- [ ] 5 Trust layer
- [ ] 6 Delivery
- [ ] 7 Scaffolded
