# ADR 0001 — Initial application stack

- **Status:** Accepted
- **Date:** 2026-10-18

## Context

PSICAS V2 is a new public project, not an upgrade of the historical application. The initial stack should therefore target currently supported versions with a reasonable support horizon and a first-party Laravel development path.

## Decision

Use the following baseline for project initialization:

- **Laravel 13.x**
- **PHP 8.5**
- **PostgreSQL 18**
- **Node.js 24 LTS**
- **Vue 3**
- **Inertia 3**
- **TypeScript**
- **Tailwind CSS** through the official Laravel Vue starter kit
- **shadcn-vue** as the starter component foundation
- **Pest 4 / PHPUnit 12-compatible tooling**
- **Laravel Pint**
- **Larastan / PHPStan** for static analysis

## Rationale

### Laravel 13

Laravel 13 is the current stable Laravel major at project initialization and remains inside its active support window.

### PHP 8.5

Laravel 13 supports PHP 8.3–8.5. PHP 8.5 is selected because it is a current stable branch with a longer remaining support horizon than PHP 8.3/8.4.

### PostgreSQL 18

PostgreSQL 18 is the current generally available major release. PostgreSQL 19 is still pre-GA at the time of this decision, so PSICAS will not base its first stable development environment on an RC.

### Node.js 24 LTS

Node 24 is an active LTS line. Node 26 is current rather than LTS, so Node 24 is preferred for reproducibility and ecosystem stability.

### Vue + Inertia

The official Laravel Vue starter kit provides Vue 3, Inertia, TypeScript, Tailwind and shadcn-vue while keeping routing, authorization and data access inside the Laravel application. This fits PSICAS better than separating the project into an API backend and an independently deployed SPA at this stage.

## Consequences

- the project remains a single deployable Laravel application;
- frontend code still has strong TypeScript/Vue structure;
- authentication scaffolding can come from Laravel's maintained starter kit;
- PostgreSQL-specific features may be used when they offer genuine value, but core business logic should stay readable and portable where practical;
- version upgrades are expected over the life of the project and should be handled through documented dependency updates.

## Revisit when

Reconsider this ADR if:

- a supported framework version becomes unsuitable before the first public release;
- the application gains a genuine need for a separately deployed frontend/API architecture;
- a deployment target imposes different runtime constraints.
