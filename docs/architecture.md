# PSICAS V2 — Architecture

## 1. Architectural direction

PSICAS V2 is a Laravel application with a Vue/Inertia frontend and PostgreSQL persistence.

The project should remain a **modular monolith** for as long as that architecture remains sufficient.

That means:

- one deployable application;
- one primary relational database;
- clear domain boundaries inside the codebase;
- no premature microservices;
- business rules kept outside controllers and views where practical.

---

## 2. Proposed application stack

### Backend

- Laravel — current stable version at project initialization;
- modern supported PHP version;
- PostgreSQL;
- queued jobs for imports and asynchronous work where useful.

### Frontend

- Vue 3;
- Inertia;
- TypeScript where it materially improves maintainability;
- a restrained component layer rather than a large UI framework dependency by default.

### Quality

- Pest / PHPUnit;
- Laravel Pint;
- Larastan/PHPStan or equivalent static analysis;
- GitHub Actions for automated checks.

### Local environment

- Docker-based development environment;
- application, PostgreSQL and supporting services defined reproducibly;
- `.env.example` with safe defaults and no secrets.

---

## 3. Domain modules

A possible top-level organization is:

```text
app/
├── Domain/
│   ├── TrainingCatalog/
│   ├── Enrollment/
│   ├── Assessment/
│   ├── Organization/
│   └── Identity/
├── Actions/
├── Http/
├── Policies/
├── Support/
└── Models/
```

This is a direction, not a rule requiring every class to live inside a complex DDD hierarchy.

The objective is readability and business separation, not architectural ceremony.

---

## 4. Domain boundaries

### TrainingCatalog

Owns public/reference training data:

- professions;
- training plans;
- training-plan versions;
- competency areas;
- competencies;
- objectives/criteria;
- source/import metadata.

### Enrollment

Owns the relationship between an apprentice and the training reference:

- apprentice enrollment;
- selected plan version;
- start/end dates;
- mentor assignments;
- enrollment status.

### Assessment

Owns progression history:

- evaluations;
- levels/scales;
- comments;
- evidence references;
- evaluation authorship;
- timestamps and history.

### Organization

Owns tenant-like organization data:

- organization profile;
- memberships;
- organizational roles;
- settings.

### Identity

Owns authentication-facing user concerns:

- account;
- authentication;
- locale;
- platform roles/permissions.

---

## 5. Initial relational model

The first migration set should roughly converge on the following relationships:

```text
organizations
    └── memberships ── users

professions
    └── training_plans
            └── training_plan_versions
                    └── competency_areas
                            └── competencies
                                    └── learning_objectives

users (apprentice)
    └── enrollments
            ├── organization
            ├── profession
            └── training_plan_version

mentor_assignments
    ├── enrollment
    └── mentor user

assessment_scales
    └── assessment_levels

assessments
    ├── enrollment
    ├── assessable item
    ├── assessment level
    └── author

assessment_evidence
    └── assessment / progression item
```

Exact table names and polymorphic relationships should be decided after validating the reference-data shapes.

---

## 6. Versioning rules

Training reference data is one of the most important architectural concerns.

### Draft

Imported or manually prepared data may be edited while a training-plan version is in draft.

### Published

Once published and used by an enrollment, structural mutation should be heavily restricted.

Corrections should generally happen by:

- explicit controlled corrections with an audit record; or
- a successor version when the change is semantically significant.

### Archived

Old versions remain readable and usable for historical enrollments but are unavailable for ordinary new enrollment.

Suggested state flow:

```text
DRAFT → PUBLISHED → ARCHIVED
```

---

## 7. Assessment history

Do not model progress as only one mutable `level` field on a competency.

Instead, progress is derived from a sequence of assessments.

Example:

```text
2026-09-01  Level 1  Mentor A
2026-11-20  Level 2  Mentor A
2027-03-12  Level 3  Mentor B
```

The current level can be queried/materialized from the most recent valid assessment, while the history stays intact.

This supports:

- auditability;
- mentor changes;
- graphs over time;
- review preparation;
- correction workflows.

---

## 8. Authorization

Authorization should be implemented with policies/gates around business ownership rather than only hiding frontend buttons.

Typical rules:

- apprentice sees only their own enrollment and allowed organization context;
- mentor sees apprentices currently assigned to them or otherwise authorized by organization policy;
- training manager can administer members and assignments in their organization;
- platform administrator can manage reference catalog publication/import operations.

Tests must cover authorization boundaries.

---

## 9. Import architecture

Importers should use adapters so one external format does not leak throughout the application.

Proposed interfaces/concepts:

```text
TrainingPlanSource
TrainingPlanImporter
NormalizedTrainingPlan
ImportSnapshot
ImportReport
```

A source adapter should be responsible for extraction/parsing.

A normalization layer should produce application-neutral DTOs before persistence.

Persistence should occur through an explicit import action/service that can compare existing versions and produce a reviewable change report.

---

## 10. Public demo strategy

The public hosted demo should use seeded fictional data.

Recommended demo personas:

- `Apprentice Demo`;
- `Mentor Demo`;
- `Training Manager Demo`.

No legacy names, emails, employers or internal references should be used.

Seeders/factories should be capable of rebuilding the complete demo state from scratch.

---

## 11. Testing strategy

### Unit/domain tests

For business rules such as:

- version transitions;
- assessment-derived current progress;
- plan assignment compatibility;
- importer normalization;
- exceptional training-plan changes.

### Feature tests

For:

- authentication;
- authorization;
- user workflows;
- assessment creation;
- catalog browsing;
- enrollment management;
- import commands/actions.

### Browser/end-to-end tests

Add only for a small number of critical flows once the UI stabilizes.

---

## 12. CI baseline

Every pull request should eventually run:

```text
composer validate
PHP lint / static analysis
Laravel Pint check
application tests
frontend lint/type-check
frontend build
```

The default branch should remain deployable after each merged change.

---

## 13. Security baseline

- no secrets committed;
- safe `.env.example` only;
- authorization enforced server-side;
- rate limiting on authentication and sensitive endpoints;
- validated uploads if/when file evidence is introduced;
- no real personal information in fixtures or screenshots;
- dependency updates monitored;
- mass-assignment and tenant-boundary tests for sensitive operations.

---

## 14. Decision log

Architecture decisions that materially constrain future implementation should be documented under `docs/adr/` as short ADRs.

Likely first ADRs:

1. modular monolith instead of microservices;
2. PostgreSQL as primary database;
3. Vue + Inertia for the application UI;
4. immutable/versioned published training references;
5. assessment history instead of mutable progress fields.
