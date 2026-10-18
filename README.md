# PSICAS

> **A modern vocational-training skills platform, rebuilt from the ground up.**

PSICAS is a new implementation of an idea I first explored during my software-development apprenticeship in 2022–2023: provide apprentices and their mentors with a clear, structured and versioned view of vocational-training skills and progression.

This repository is **not a continuation of the historical codebase**. PSICAS V2 starts from a clean architecture and is intended to become a production-quality Laravel application with real domain modelling, automated tests, CI, reproducible development tooling and up-to-date public training-plan data.

## Project status

🚧 **Foundation phase**

The product scope, architecture, official data-source strategy and initial runtime stack are defined. The next step is application initialization.

## Product goals

PSICAS should make it possible to:

- manage professions and vocational training plans;
- preserve multiple versions of a training plan over time;
- structure competency areas, competencies and learning objectives;
- assign an apprentice to the correct training-plan version;
- track progression and evaluations over time;
- let apprentices and mentors work from role-specific dashboards;
- keep a reliable history instead of overwriting past evaluations;
- import and normalize public official training-plan data where possible;
- expose only fictional/demo personal data in the public repository.

## Initial stack

- **Backend:** Laravel 13 / PHP 8.5
- **Frontend:** Vue 3 + Inertia 3 + TypeScript
- **UI foundation:** Tailwind CSS + shadcn-vue
- **Database:** PostgreSQL 18
- **Node.js:** 24 LTS
- **Testing:** Pest / PHPUnit
- **Quality:** Laravel Pint + static analysis
- **Environment:** Docker
- **CI:** GitHub Actions

The rationale is documented in [`ADR 0001`](docs/adr/0001-application-stack.md).

## Architecture principles

- domain-first data modelling;
- immutable/versioned training-plan structures;
- clear authorization boundaries;
- auditability of meaningful business changes;
- reproducible local setup;
- automated tests for business rules;
- no dependency on private legacy data.

## Documentation

- [`docs/product-spec.md`](docs/product-spec.md) — functional scope and product rules
- [`docs/architecture.md`](docs/architecture.md) — technical architecture and domain model
- [`docs/data-sources.md`](docs/data-sources.md) — official Swiss training-data source strategy
- [`docs/roadmap.md`](docs/roadmap.md) — implementation phases
- [`docs/adr/`](docs/adr/) — architecture decision records

## Legacy project

The original apprenticeship project is preserved separately as a **private historical archive**. It is useful as a record of the initial idea and of my technical progression, but none of its Git history, private documentation or internal data is required by PSICAS V2.

---

<details>
<summary><strong>🇫🇷 Version française</strong></summary>

### À propos

PSICAS est une nouvelle implémentation d'une idée que j'avais commencée pendant mon apprentissage de développeur : centraliser le suivi des compétences d'un apprenti et rendre sa progression lisible pour lui comme pour son encadrement.

Cette V2 repart volontairement de zéro. L'objectif n'est pas de moderniser superficiellement l'ancien code mais de concevoir un véritable produit métier avec une architecture propre, des plans de formation versionnés, des tests automatisés, une CI et des données publiques officielles lorsqu'elles sont disponibles.

Le projet historique reste archivé séparément et privé.

</details>
