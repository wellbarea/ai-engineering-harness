# Project Context

> **Template** — replace every `[PLACEHOLDER]` with your project's real values.
> This file is loaded by agents before acting; keep it accurate and up to date.

## Project

Name: [YOUR PROJECT NAME]

## Description

[One paragraph: what the product does, who it serves, and the main
technical approach. Include whether it's SaaS/multi-tenant, the primary
deployment target, and any hard constraints (e.g. "runs entirely at the
edge", "no external API calls from client").]

## Business Domain

[Describe the industry and operational domain. What real-world activities
does the software model? What are the stakes of errors (financial, legal,
safety)?]

## Users

- **[Role 1]** — [what they do in the app]
- **[Role 2]** — [what they do in the app]
- **[Role 3]** — [what they do in the app]

[Note any multi-tenancy: how users are isolated, what the tenant boundary is.]

## Frontend

Framework: [e.g. Next.js 15 App Router / React 19 / SvelteKit]

Language: TypeScript

UI: [e.g. shadcn/ui + Tailwind CSS v4]

State management: [e.g. Zustand / Jotai / none]

Data fetching: [e.g. React Query + Server Components / tRPC / SWR]

Forms: [e.g. react-hook-form + zod / useActionState]

Testing: [e.g. Vitest + Testing Library / Playwright / none configured]

## Backend

Framework: [e.g. Next.js Server Actions / Express / Hono / none (edge functions)]

Language: TypeScript

Database: [e.g. Supabase (Postgres) with RLS / PlanetScale / Prisma + Neon]

Authentication: [e.g. Supabase Auth / NextAuth / Clerk]

Testing: [e.g. Vitest with real DB / none configured]

## Infrastructure

Hosting: [e.g. Vercel / Cloudflare Workers / Fly.io]

CI/CD: [e.g. GitHub Actions on push to main / manual deploy]

## Repository

Git provider: GitHub

Branch strategy: [e.g. trunk-based / feature branches / gitflow]

## Important Constraints

- [Constraint 1: e.g. "All data is isolated by tenant_id enforced at the database level — no query may rely on application-layer filtering alone"]
- [Constraint 2: e.g. "No new npm dependency may be added without architectural approval"]
- [Constraint 3: e.g. "All writes to financial tables must go through stored procedures, not direct INSERT/UPDATE"]

## Non-Goals

- [What this product explicitly does not do]
- [Scope boundaries that often tempt feature creep]

## Important Conventions

- [Convention 1: e.g. "lib/data/*.ts is read-only server code; actions.ts handles writes"]
- [Convention 2: e.g. "All Server Actions validate input with zod.safeParse before any DB call"]
- [Convention 3: e.g. "New tables always get RLS policies in the same migration"]
