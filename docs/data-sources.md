# PSICAS V2 — Authoritative training-data sources

## Goal

PSICAS should use authoritative public Swiss vocational-training sources wherever possible, while keeping import logic independent from any single website layout.

The first implementation target is **initial vocational education and training (VET)** leading to:

- CFC / EFZ;
- AFP / EBA.

Higher vocational education is outside the initial scope.

---

## 1. Primary catalog source — SEFRI / SBFI Berufsverzeichnis

The Swiss State Secretariat for Education, Research and Innovation (SEFRI/SBFI) maintains the official **Liste des professions / Berufsverzeichnis (BVZ)**.

Primary entry points:

- https://www.sbfi.admin.ch/fr/liste-des-professions
- https://www.becc.admin.ch/becc/public/bvz/

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

---

## 4. Secondary discovery source — orientation.ch

https://www.orientation.ch/ provides a public profession-discovery experience commissioned by the cantons and supported by the Confederation.

It is useful for:

- human-friendly profession discovery;
- descriptions and contextual metadata;
- validation of public-facing labels.

It should not replace SEFRI/Fedlex as the legal/reference authority for PSICAS plan versioning.

---

## 5. Import strategy

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

training_plan_versions
- ...
- source_snapshot_id
- valid_from
- valid_until
- published_at
```

Import flow:

```text
SEFRI profession catalog
        ↓
Discover official profession + responsible organization
        ↓
Fetch ordinance metadata from official source
        ↓
Fetch detailed training plan from responsible organization
        ↓
Normalize
        ↓
Validate
        ↓
Create/update DRAFT version
        ↓
Human review
        ↓
PUBLISH
```

---

## 6. Important rule: do not promise universal automatic parsing initially

Training plans may be distributed as PDFs and their structures differ by profession and revision year.

The V1 importer should therefore be designed for **source-specific adapters**, not an unreliable generic PDF scraper.

A realistic progression is:

1. import the official profession catalog;
2. implement one complete profession family end-to-end;
3. formalize the normalized schema;
4. add adapters incrementally;
5. only automate formats that are stable enough to test reproducibly.

---

## 7. First reference implementation

The recommended first real dataset is the Swiss ICT profession family because:

- it is directly relevant to the project's history;
- the responsible organization publishes structured training documentation publicly;
- it provides a realistic competency-oriented plan for validating the PSICAS domain model.

The first end-to-end target should be one currently valid ICT CFC profession, followed by the other ICT professions once the importer model is proven.
