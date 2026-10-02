# Web App Architecture & Tech Stack

> **Ticket:** [rob-bl8ke/work-diary#18](https://github.com/rob-bl8ke/work-diary/issues/18)
> **Date:** 2026-10-02

---

## Decision Summary

The work-diary app is a locally-served, npm-workspaces monorepo with a React frontend (Vite) and a NestJS API server. The two processes share TypeScript interfaces through a `packages/shared` package.

---

## Repository Layout

```
work-diary/
├── package.json            ← workspace root (concurrently, scripts)
├── packages/
│   ├── ui/                 ← Vite + React frontend
│   ├── api/                ← NestJS API server
│   └── shared/             ← shared TypeScript interfaces (DTOs, domain types)
└── docs/
```

`npm workspaces` wires the three packages; a single `npm install` at the root resolves all dependencies.

---

## Frontend — `packages/ui`

| Concern | Choice |
|---|---|
| Bundler / dev server | Vite |
| UI framework | React (TypeScript) |
| Component library | shadcn/ui (copy-into-project, Radix primitives) |
| Styling | Tailwind CSS v4 |
| Markdown editor | Monaco (per `docs/uix.md`) |
| Shell layout | Three-zone: nav \| main \| optional context drawer (per `docs/uix.md`) |
| HTTP / data-fetching | TanStack Query v5 + native `fetch` |

shadcn/ui's copy-in model means every component is local code; the `docs/uix.md` colour token system (`--canvas`, `--accent`, etc.) maps directly onto Tailwind's CSS custom property layer with no upstream library to fight.

TanStack Query handles caching, loading states, and background refetch across a read-heavy document app without manual state management.

---

## Backend — `packages/api`

| Concern | Choice |
|---|---|
| Framework | NestJS (Express adapter) |
| File-system access | Thin `FileService` — `fs/promises` directly, no extra abstraction |
| Search index | `better-sqlite3` + raw SQL (SQLite FTS5 virtual tables) |
| Content root | `WORK_DIARY_ROOT` env var → `--root` CLI flag → `process.cwd()` |
| Authentication | None for v1 — localhost-only; documented constraint |

`better-sqlite3` is synchronous and zero-dependency at runtime. FTS5 `MATCH` queries are written in raw SQL regardless of ORM choice, so removing the ORM removes one layer of indirection. The `FileService` is a plain NestJS injectable; the NestJS module/service boundary is the seam — no repository interface needed until there is a concrete second implementation to slot in.

**v1 auth constraint:** The API server binds to `localhost` only. No token, no login. This constraint must be revisited before any non-local deployment.

---

## Type Sharing — `packages/shared`

Domain interfaces (`Event`, `Artifact`, `Snapshot`, API request/response DTOs) live in `packages/shared` and are imported by both `ui` and `api`. Types are maintained by hand; no OpenAPI code-generation step in v1.

---

## Startup

### Daily use (dev mode)

```bash
npm start
# root package.json script:
# concurrently "npm run dev -w packages/api" "npm run dev -w packages/ui"
```

`packages/api` runs `nest start --watch`; `packages/ui` runs `vite`.

### Debug sessions

A VS Code compound launch task starts both processes and attaches the debugger. Shares the same environment variables as the `npm start` path.

### Production / clean run

```bash
npm run build
# Vite compiles packages/ui → packages/api/public/
# NestJS ServeStaticModule serves the compiled output
node packages/api/dist/main.js
```

A single Node process in production. Required before any small-team deployment.

---

## Constraints Carried Forward

- Localhost-only for v1 — revisit before team deployment.
- Auth design (single-token or session-based) is a prerequisite for the small-team extension ticket.
- Component library choices and visual tokens must stay aligned with `docs/uix.md`; consult it before any UI component decision.
