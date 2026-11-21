# SocialOS

Portfolio-grade social media management SaaS (scheduling, approvals, analytics), built by a solo developer.
Non-commercial project. Goal: demonstrate senior-level backend engineering. Quality over speed.

Source of truth for architecture: `docs/adr/` and `docs/architecture/`. Never contradict an accepted ADR.
If a change needs a different decision, stop and propose a new ADR instead of coding around it.

## Stack

- .NET 10 (LTS), ASP.NET Core Minimal APIs, EF Core + Npgsql, PostgreSQL, Redis, .NET Aspire for local orchestration
- Frontend in `web/`: React + Vite + TypeScript (strict), TanStack Query, Tailwind + shadcn/ui
- Tests: xUnit v3, Shouldly, Testcontainers, WireMock.Net, architecture tests
- Deploy: Docker images for `linux/arm64`, Docker Compose + Caddy on one Oracle Cloud VM; Cloudflare R2 for media and backups

## Repository layout

- `src/` — `SocialOS.AppHost`, `SocialOS.ServiceDefaults`, `SocialOS.Api`, `SocialOS.Worker`, `SocialOS.PlatformSimulator`, `SocialOS.BuildingBlocks`, `Modules/<Module>/`, `Integrations/<Provider>/`
- `web/` — React app
- `tests/` — `UnitTests`, `IntegrationTests`, `ArchitectureTests`
- `docs/` — `adr/`, `architecture/`, `product/`, `runbooks/`
- `deploy/` — Compose files, Caddyfile

## Commands

- Run everything locally: `dotnet run --project src/SocialOS.AppHost`
- Build: `dotnet build -warnaserror`
- Test: `dotnet test` (integration tests need Docker running)
- Format check: `dotnet format --verify-no-changes`
- Frontend (from `web/`): `npm run dev`, `npm run build`, `npm test`, `npm run lint`
- Add commands here as they are established. Do not guess commands that are not listed.

## Architecture rules

- Modular monolith. Modules talk to each other only through their `Contracts` namespace; everything else is `internal`. Architecture tests enforce this.
- Vertical slices inside a module: `Features/<UseCase>/` holds the endpoint, handler and validator together.
- No MediatR, no AutoMapper, no generic repository. Use `DbContext` directly and map by hand.
- IMPORTANT: Every tenant-owned table has `organization_id`. Isolation = EF global query filter + composite FK `(organization_id, id)` + PostgreSQL RLS. Never call `IgnoreQueryFilters()` outside reviewed infrastructure code.
- IDs are UUIDv7 (`Guid.CreateVersion7()`). Times are `DateTimeOffset` in UTC, stored as `timestamptz`. Status columns are `text` + `CHECK`, not Postgres enums.
- Background work runs only through the `publishing.jobs` table (SKIP LOCKED + lease). Never use `Task.Run`, timers or in-memory queues for business work.
- Social platforms are reached only through `ISocialPublisher` adapters. Modules never call platform APIs directly.
- Expected failures return a `Result`; unexpected ones throw. HTTP errors are RFC 9457 Problem Details.
- Secrets never go into the repo. Never log tokens, passwords or full email addresses.

## Code style

- Nullable enabled, warnings are errors. File-scoped namespaces. Classes `sealed` unless designed for inheritance.
- Comments explain why, not what. No commented-out code. No XML doc comments on internal types unless behaviour is non-obvious.
- Descriptive names; no `Manager`, `Helper` or `Util` classes.
- Keep diffs focused on the task. Do not refactor unrelated code in the same change.
- Do not add a dependency without stating why it is needed and what the alternative was.

## Testing

- Each feature: unit tests for domain rules, plus an integration test through HTTP against real PostgreSQL (Testcontainers). Never mock the database.
- Every tenant-owned endpoint has a two-tenant isolation test.
- No placeholder tests (`Assert.True(true)`). A test must be able to fail for a real reason.
- Test names: `Action_Condition_ExpectedResult`.

## Workflow

- Work starts from a GitHub issue. Branches: `feat/<issue>-<slug>`, `fix/…`, `chore/…`, `docs/…`.
- Conventional Commits, e.g. `feat(publishing): claim jobs with SKIP LOCKED`. Each commit builds.
- Before calling a task done: build, tests and format check pass, and you show the commands and their output. Update docs or ADRs if a decision changed.
- Do not invent product requirements. If the issue or docs do not answer a question, ask.
- When a package version, API shape or platform rule matters, verify it in official docs instead of relying on memory.
- When compacting, preserve: current issue number, files changed, test commands, open decisions.

## Language

Code, comments, commits, docs and UI text are in English. The developer may write in Turkish; reply in Turkish, write artifacts in English.
