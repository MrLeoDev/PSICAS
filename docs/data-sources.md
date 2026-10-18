# PSICAS V2 — Authoritative training-data sources

## Goal

PSICAS should use authoritative public Swiss vocational-training sources wherever possible, while keeping import logic independent from any single website layout.

The first product scope is **Swiss initial vocational education and training (VET)** leading to:

- CFC / EFZ;
- AFP / EBA.

Higher vocational education (BP, DF/HFP, ES and related programmes) is outside V1 unless the domain model is deliberately extended later.

---

## Coverage target

The long-term catalog target is **all currently active Swiss CFC/EFZ and AFP/EBA professions listed by SEFRI/SBFI**, not only ICT professions.

PSICAS should distinguish three levels of coverage:

### 1. Catalog coverage

For every active initial-VET profession we want at least:

- official profession identifier/code where available;
- CFC/EFZ or AFP/EBA level;
- official title in available national languages;
- duration and specialization/direction metadata when applicable;
- current/active status;
- responsible organization;
- official source links;
- ordinance metadata;
- current training-plan reference.

This layer should aim for comprehensive national coverage.

### 2. Structured plan coverage

For each profession/version we progressively normalize the content required by PSICAS:

- competency areas;
- operational competencies;
- objectives/criteria where the official plan exposes them;
- training-location metadata where useful;
- localized labels;
- version/effective dates.

Unlike the catalog layer, this cannot be assumed to be universally machine-readable because official plans are published by many different organizations and in different document structures.

### 3. Historical/version coverage

PSICAS should preserve superseded versions when they are needed to represent apprentices who started training under an earlier plan.

We do **not** need to ingest every historical Swiss training document since the 1930s for V1. Priority is:

1. every currently active profession;
2. every current plan version;
3. predecessor versions still relevant because learners may legally complete training under them;
4. older history only when useful or specifically imported.

This gives us meaningful versioning without turning the first release into a national archive project.

---

## 1. Primary catalog source — SEFRI / SBFI Berufsverzeichnis

The Swiss State Secretariat for Education, Research and Innovation (SEFRI/SBFI) maintains the official **Liste des professions / Berufsverzeichnis (BVZ)**.

Primary entry points:

- https://www.sbfi.admin.ch/fr/liste-des-professions
- https://www.becc.admin.ch/becc/public/bvz/

The SEFRI describes this directory as the overview of professions currently offered and governed by the Vocational and Professional Education and Training Act.

This should be treated as the primary source for determining which regulated professions/qualifications are currently offered and for discovering their responsible organizations and official reference documents.

### Intended PSICAS usage

- profession identity;
- official profession number/code when available;
- CFC/AFP qualification type;
- localized profession titles;
- active/current catalog status;
- responsible professional organization;
- links to current training regulations and plans.

### Import note

The public BVZ interface is an application rather than a documented public API. PSICAS should therefore isolate access behind a source adapter and must not couple core domain models to the current page structure.

A catalog synchronization must produce a reviewable diff instead of silently deleting records when a source page changes.

---

## 2. Legal source — Fedlex

Fedlex is the official publication platform for Swiss federal law.

Entry point:

- https://www.fedlex.admin.ch/

Vocational-training ordinances published by SEFRI define the legal framework, dates, profession designation and references to the corresponding training plan.

### Intended PSICAS usage

- ordinance identifier / SR-RS reference;
- publication and effective dates;
- legal validity metadata;
- transitional provisions;
- authoritative cross-check for plan-version lifecycle.

This matters because a new ordinance can enter into force while apprentices who started under the former rules remain entitled to complete under the previous regime.

Fedlex is **not** expected to contain the full operational competency hierarchy for every profession. It complements rather than replaces the training plan itself.

---

## 3. Training plans — responsible professional organizations (OrTra / OdA)

The detailed training plan is generally produced by the competent organization of the world of work and approved by SEFRI.

These documents contain the information PSICAS needs most:

- profession profile;
- operational competency areas;
- operational competencies;
- expected training content;
- distribution across training locations where applicable.

Because the responsible organization differs by profession, PSICAS must support multiple adapters and source formats.

Example for ICT professions:

- https://www.ict-berufsbildung.ch/

### Important publication rule

PSICAS should normally persist **normalized structured facts plus provenance/source URLs**, not blindly mirror every original PDF/DOCX into the repository.

Before redistributing original documents, their applicable publication/reuse conditions must be checked. A public source does not automatically mean every document should be republished by PSICAS.

---

## 4. Historical profession development

SEFRI also provides a database describing profession development under earlier federal vocational-training laws, while developments from 2004 onward are represented through the current profession directory.

This can later help PSICAS model predecessor/successor relationships between professions.

For V1 it is a secondary historical source, not a prerequisite for launching the current national catalog.

---

## 5. Secondary discovery source — orientation.ch

https://www.orientation.ch/ provides a public profession-discovery experience commissioned by the cantons and supported by the Confederation.

It is useful for:

- human-friendly profession discovery;
- descriptions and contextual metadata;
- validation of public-facing labels.

It should not replace SEFRI/Fedlex as the legal/reference authority for PSICAS plan versioning.

---

## 6. Open-data discovery

`opendata.swiss` exposes a CKAN API for discovering Swiss public datasets and their metadata.

It can be useful when an authoritative source publishes a reusable machine-readable dataset, but PSICAS must still verify the publisher and provenance of the concrete dataset rather than treating the opendata catalog itself as the vocational-training authority.

---

## 7. Proposed persistence model

PSICAS should maintain source provenance for every imported training-plan version.

Proposed records:

```text
reference_sources
- id
- type
- authority
- base_url

import_snapshots
- id
- reference_source_id
- external_identifier
- source_url
- retrieved_at
- content_hash
- raw_metadata

professions
- id
- official_code
- qualification_type
- status

training_plans
- id
- profession_id

training_plan_versions
- id
- training_plan_id
- source_snapshot_id
- valid_from
- valid_until
- published_at
- lifecycle_status
```

The exact schema will be finalized against real source shapes before migrations are frozen.

---

## 8. Import flow

```text
SEFRI profession catalog
        ↓
Discover all active CFC/AFP professions
        ↓
Create/update catalog records
        ↓
Discover ordinance + official training-plan sources
        ↓
Fetch source metadata/content
        ↓
Create immutable import snapshot
        ↓
Source-specific extraction
        ↓
Normalize to PSICAS DTOs
        ↓
Validate
        ↓
Compare with existing version
        ↓
Create/update DRAFT version
        ↓
Human review
        ↓
PUBLISH
```

No source synchronization should directly mutate a published training-plan structure without an explicit review/versioning workflow.

---

## 9. Import status per profession/version

To support national coverage honestly, each plan version should expose an import/completeness status such as:

```text
DISCOVERED
METADATA_IMPORTED
SOURCE_CAPTURED
STRUCTURED
REVIEWED
PUBLISHED
```

This lets PSICAS say, for example, that 100% of active professions are known to the catalog even while detailed competency normalization is still being completed profession by profession.

---

## 10. Important rule: do not promise universal automatic parsing

Training plans may be distributed as PDFs and their structures differ by profession and revision year.

The importer should therefore be designed for **source-specific adapters**, not an unreliable generic PDF scraper.

A realistic progression is:

1. import the complete active CFC/AFP profession catalog;
2. implement one complete profession family end-to-end;
3. formalize the normalized schema;
4. add adapters incrementally;
5. use assisted/manual normalization when a source format is unstable;
6. automate formats only when they are stable enough to test reproducibly.

The project should prefer correct, traceable data over claiming fake 100% automation.

---

## 11. First reference implementation

The recommended first fully structured dataset is the Swiss ICT profession family because:

- it is directly relevant to the project's history;
- the responsible organization publishes detailed training documentation publicly;
- it provides a realistic competency-oriented plan for validating the PSICAS domain model.

The first end-to-end target should be one currently valid ICT CFC profession, followed by the other ICT professions once the importer model is proven.

This is only the **first detailed adapter**, not a restriction of the catalog: the catalog itself should target all current Swiss CFC/AFP professions from the beginning.
