# PSICAS V2 — Development environment

## Goal

The local development environment should be reproducible on macOS, Linux and CI-friendly systems without requiring developers to install PostgreSQL, Nginx or project-specific PHP extensions directly on the host.

Docker is an implementation detail of development and deployment tooling; the application itself must remain a normal Laravel project.

---

## Planned Docker Compose services

### `app`

PHP application runtime.

Planned responsibilities:

- PHP 8.5 FPM;
- Composer;
- Laravel CLI / Artisan;
- required PHP extensions such as `pdo_pgsql`, `intl`, `bcmath`, `pcntl`, `zip`, `gd`/image support as required;
- application source mounted for development;
- non-root execution where practical.

### `web`

Nginx development web server.

Responsibilities:

- serve Laravel's `public/` directory;
- forward PHP requests to `app`;
- provide predictable HTTP configuration independent from the host machine.

### `postgres`

PostgreSQL 18.

Responsibilities:

- primary local application database;
- persistent named volume;
- health check;
- safe development-only credentials configured through `.env`.

### `node`

Node.js 24 LTS development/build service.

Responsibilities:

- install frontend dependencies;
- Vite development server;
- frontend lint/type checks;
- production asset builds.

The frontend toolchain should not need a globally installed Node version on the host.

### `mailpit`

Development-only local mail catcher.

Responsibilities:

- receive application emails without sending real messages;
- web UI for testing authentication/notification emails.

### Redis

Redis is intentionally **not required on day one**. Laravel database-backed cache/session/queue support is sufficient for the first milestones.

Redis can be introduced when the application has a concrete need for higher-throughput queues, cache semantics or real-time infrastructure. This avoids adding infrastructure only for appearance.

---

## Planned repository layout

```text
PSICAS/
├── compose.yaml
├── docker/
│   ├── php/
│   │   └── Dockerfile
│   └── nginx/
│       └── default.conf
├── .env.example
├── Makefile
└── ... Laravel application
```

---

## Developer experience

The target workflow is deliberately small:

```bash
make setup
make up
make test
make quality
make down
```

Equivalent `docker compose` commands will remain usable directly; the Makefile is only a convenience layer.

### `make setup`

Expected to:

1. build containers;
2. install Composer dependencies;
3. install Node dependencies;
4. create `.env` from `.env.example` when absent;
5. generate an application key;
6. start PostgreSQL;
7. run migrations;
8. seed safe fictional demo/reference data as appropriate.

### `make test`

Runs the automated PHP test suite in the application environment.

### `make quality`

Runs formatting checks, static analysis, frontend lint/type checks and related quality gates.

---

## Volumes and permissions

- application source stays on the host and is bind-mounted for development;
- PostgreSQL data uses a Docker named volume;
- generated dependency/cache directories may use volumes when useful for performance;
- containers should avoid writing host files as `root`;
- host UID/GID mapping will be supported where necessary for Linux compatibility.

macOS should require no special permission workaround beyond normal Docker Desktop/compatible runtime file sharing.

---

## Secrets

No real secrets belong in Dockerfiles, Compose files or committed environment files.

`.env.example` will contain development-safe placeholders only.

Local `.env` remains ignored by Git.

GitHub Actions secrets, if eventually needed for deployment, will be configured in GitHub rather than committed.

---

## Health checks

Infrastructure services should expose health checks where practical so `depends_on` does not merely mean "container process started".

At minimum PostgreSQL should become healthy before database-dependent initialization is attempted.

---

## CI relationship

Docker will define the supported local environment, but CI does not have to boot the complete local Compose stack for every check.

GitHub Actions may run PHP/Node directly with a PostgreSQL service container when that is faster and clearer.

The important contract is that the same application tests and quality commands pass both locally and in CI.

---

## Why not Laravel Sail only?

Laravel Sail is a good development tool and may be used as a reference, but PSICAS intends to keep a small explicit Docker setup because it:

- makes the infrastructure visible and understandable in a portfolio project;
- lets us pin the exact PHP/PostgreSQL/Node setup we want;
- avoids carrying services we do not use;
- provides a straightforward path from local development to CI/deployment knowledge.

The Docker setup should stay intentionally boring and small rather than becoming a second project of its own.
