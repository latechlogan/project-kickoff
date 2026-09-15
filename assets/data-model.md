# {{PROJECT_NAME}} — Data and state

<!-- Written by project-kickoff, Phase 3. Read when touching persistence, schema, fixtures, or anything that stores state. -->

## Entities

```mermaid
erDiagram
    {{ENTITY_A}} ||--o{ {{ENTITY_B}} : {{RELATION}}
```

| Entity | Purpose | Key fields |
|---|---|---|
| {{ENTITY}} | {{WHAT_IT_REPRESENTS}} | {{FIELDS}} |

## Stored vs derived

| Data | Stored or derived | Derived from | Why |
|---|---|---|---|
| {{THING}} | {{STORED_OR_DERIVED}} | {{SOURCE}} | {{REASON}} |

<!-- Derived data that gets stored needs a stated reason (caching, audit) and a stated invalidation. Otherwise it's a bug factory. -->

## Schema and migrations

- **Where the schema lives:** {{PATH}}
- **Migration tool:** {{TOOL_OR_NONE}}
- **Who runs migrations, and when:** {{PROCESS}}

## Fixtures and seed data

- **Tests run against:** {{IN_MEMORY_OR_TEST_DB_OR_FILE}}
- **Fixtures live at:** {{PATH}}
- **Regenerate with:** `{{COMMAND}}`

## In-memory / file state

<!-- Delete if all state is in the database above. -->

| State | Shape | Read by | Written by |
|---|---|---|---|
| {{STATE}} | {{TYPE}} | {{MODULE}} | {{MODULE}} |
