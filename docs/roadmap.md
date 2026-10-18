# PSICAS V2 — Roadmap

This roadmap intentionally starts with product/domain clarity before UI work.

## Phase 0 — Foundation

- [x] create clean public repository;
- [x] document product vision;
- [x] define initial architecture;
- [x] identify authoritative public training-plan sources;
- [x] choose exact supported Laravel/PHP/Node versions;
- [ ] initialize Laravel + Vue + Inertia + PostgreSQL;
- [ ] add Docker development environment;
- [ ] add CI baseline;
- [ ] add code quality tooling.

**Exit condition:** a clean checkout boots locally and CI is green.

---

## Phase 1 — Training catalog

- [ ] professions;
- [ ] training plans;
- [ ] training-plan versions;
- [ ] competency hierarchy;
- [ ] localized labels;
- [ ] draft/published/archived lifecycle;
- [ ] catalog browsing UI;
- [ ] factories/seeders;
- [ ] tests for versioning rules.

**Exit condition:** PSICAS can represent at least one real current public training plan without using legacy private data.

---

## Phase 2 — Organizations and identities

- [ ] authentication;
- [ ] organizations;
- [ ] memberships;
- [ ] apprentice / mentor / training-manager roles;
- [ ] authorization policies;
- [ ] organization settings;
- [ ] demo personas;
- [ ] permission tests.

**Exit condition:** demo users have isolated role-appropriate access.

---

## Phase 3 — Enrollment

- [ ] apprentice enrollment;
- [ ] profession + plan-version assignment;
- [ ] training start/end dates;
- [ ] mentor assignments with history;
- [ ] enrollment status;
- [ ] guarded plan-version change workflow;
- [ ] enrollment tests.

**Exit condition:** a fictional apprentice can be enrolled into a specific immutable training-plan version.

---

## Phase 4 — Assessments and progression

- [ ] configurable assessment scales;
- [ ] competency/objective evaluations;
- [ ] assessment comments;
- [ ] historical evaluation timeline;
- [ ] derived current progress;
- [ ] evidence references;
- [ ] apprentice progression UI;
- [ ] mentor evaluation UI;
- [ ] business-rule tests.

**Exit condition:** a mentor can evaluate an apprentice repeatedly without losing history.

---

## Phase 5 — Dashboards

- [ ] apprentice dashboard;
- [ ] mentor dashboard;
- [ ] training-manager dashboard;
- [ ] progress summaries;
- [ ] attention/action queues;
- [ ] recent activity;
- [ ] responsive polish.

**Exit condition:** each role gets a useful landing page with meaningful data.

---

## Phase 6 — Official source import

- [ ] source registry;
- [ ] raw import snapshots;
- [ ] first source adapter;
- [ ] normalization DTOs;
- [ ] import validation;
- [ ] change detection;
- [ ] review-before-publish workflow;
- [ ] source attribution metadata;
- [ ] importer tests/fixtures.

**Exit condition:** at least one current plan can be recreated reproducibly from an authoritative public source.

---

## Phase 7 — Portfolio/public release

- [ ] polished README;
- [ ] architecture diagrams;
- [ ] screenshots;
- [ ] hosted demo environment;
- [ ] seeded fictional demo organization;
- [ ] legacy-project story / evolution section;
- [ ] security/privacy review;
- [ ] release notes;
- [ ] `v1.0.0` tag.

**Exit condition:** a recruiter can understand, run and evaluate the project without private context.

---

## Later possibilities

Not committed for v1:

- PDF/Excel progress exports;
- notifications/reminders;
- richer evidence/file storage;
- competency comparison and reporting;
- API integrations;
- SSO;
- multi-organization SaaS features;
- audit-log administration UI;
- advanced analytics.
