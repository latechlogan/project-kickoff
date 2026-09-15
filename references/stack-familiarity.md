# Stack familiarity

The user's stated familiarity by tool, as of September 2026. This is an input to Phase 2, not a default: propose the right tool for the project, then weigh this openly. The question it answers is *can the user read the code the agent writes* — because reading is how review happens when agents do the writing.

Two levels matter. **Writes** means the user could have written it themselves and will catch mistakes in it. **Reads** means they can follow it fluently without having written much of it; review at the architecture level still works. Anything below "reads" is a real cost: the user can't verify the code, only the tests.

Maintained by hand by the user. The skill never edits this file.

## Writes

| Tool | Note |
|---|---|
| Astro | Strong preference; enjoys it |
| React | |
| Node.js | |
| Express | |
| SQL / PostgreSQL | |
| Jest | Came naturally |
| GitHub | |

## Reads

| Tool | Note |
|---|---|
| Hono | Close enough to Express that agent-written Hono is easy to follow |
| Tailwind | Follows it fine; writing it feels slow. Any CSS approach is fine when the agent writes it |
| Prisma | The only ORM used; not enough depth to prefer it over alternatives |
| Cloudflare | Has been fine |
| Next.js | Fine, but not liked much — prefer Astro when it fits |

## Untested

| Tool | Note |
|---|---|
| Vitest | Never used. Jest-compatible API, so likely lands in "reads" — say so when proposing it |

## Struggled with

| Tool | Note |
|---|---|
| React Testing Library | A struggle. If component tests are needed, name the alternative or the cost |
| Supabase | A struggle. Prefer plain PostgreSQL where the project allows |

## Hosting

| Tool | Note |
|---|---|
| Netlify | Liked |
| Railway | Liked |
| Cloudflare | Fine |
| Vercel | Not a favorite — propose only when it's clearly the right fit, and say why |

## How to use this in Phase 2

- Propose within Writes and Reads by default. That is not a concession; it's what keeps architecture-level review possible.
- When a tool outside those rows is clearly better for the problem, propose it anyway, name the trade ("you won't be able to verify this layer by reading it; the trust layer carries more weight here"), and let the user choose.
- When familiarity is the deciding factor between two otherwise-close options, say that in one sentence. The user should never wonder why a stack was picked.
- A tool moving between rows is a `[learning]` line in the project's DECISIONS.md and, if the user wants it to stick, a hand edit here.
