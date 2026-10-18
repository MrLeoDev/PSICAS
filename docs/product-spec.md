# PSICAS V2 — Product specification

## 1. Product vision

PSICAS is a vocational-training skills platform for apprentices, mentors and training organizations.

Its purpose is to answer three questions clearly:

1. **What must this apprentice learn?**
2. **Where are they today?**
3. **How did their progression evolve over time?**

The application must treat the training plan as a versioned business reference, not as editable profile data.

---

## 2. Primary users

### Apprentice

Can:

- see the training plan assigned to them;
- understand competency areas, competencies and objectives;
- see their current progression;
- submit evidence, notes or self-assessments where enabled;
- consult past evaluations and comments;
- see upcoming or incomplete items.

Cannot modify the official structure of a training plan.

### Mentor / trainer

Can:

- follow assigned apprentices;
- evaluate competencies and objectives;
- add comments and evidence references;
- inspect the history of previous evaluations;
- compare progress against the expected training structure;
- prepare periodic reviews.

### Training manager

Can:

- manage users and assignments within an organization;
- assign mentors to apprentices;
- select the appropriate profession and training-plan version;
- configure organization-specific settings without altering official source data.

### Platform administrator

Can:

- import and publish reference training plans;
- manage source metadata and normalization rules;
- correct reference-data issues through controlled revisions;
- manage platform-level configuration.

---

## 3. Core domain

### Profession

Represents a vocational profession or qualification.

Examples of attributes:

- official code when available;
- localized title;
- qualification type;
- active/inactive state.

### Training plan

Represents the conceptual plan attached to a profession.

A plan is never assumed to have a single permanent structure.

### Training-plan version

Represents one published version of a plan.

Important rules:

- versions have validity dates when known;
- an apprentice is assigned to a specific version;
- publishing a newer version does **not** migrate existing apprentices automatically;
- historical versions remain readable;
- published reference structures should not be destructively edited.

### Competency area

High-level grouping inside a training-plan version.

### Competency

A capability to be developed and evaluated.

### Learning objective / criterion

A more granular expected outcome beneath a competency.

The final hierarchy will follow the structure of the official source data instead of forcing every profession into an artificial schema.

---

## 4. Apprentice lifecycle

An apprentice belongs to an organization and is enrolled in:

- one profession;
- one training-plan version;
- a training period with start/end dates.

They may have one or several mentors over time.

Changing mentor must preserve previous evaluations and authorship.

Changing training-plan version is an explicit exceptional operation and must be auditable.

---

## 5. Evaluation model

An evaluation must be historical, not overwritten in place.

A useful conceptual model is:

```text
Competency / objective
        ↓
Evaluation
        ↓
Level + comment + author + date
        ↓
Optional evidence
```

Possible rating models may differ by plan. The application must therefore avoid hard-coding one universal four-level scale into the reference model.

The first product version may provide a default configurable scale for demo and organization-specific use.

---

## 6. Evidence

An apprentice or mentor may attach evidence to progression items.

Examples:

- project reference;
- external URL;
- text note;
- document metadata.

Initial scope should avoid storing sensitive production documents in the public demo environment.

---

## 7. Dashboards

### Apprentice dashboard

Should prioritize:

- current overall progression;
- competency areas requiring attention;
- recent evaluations;
- next objectives;
- evidence awaiting review.

### Mentor dashboard

Should prioritize:

- apprentices requiring action;
- recent submissions;
- evaluations due;
- progression anomalies;
- quick access to each apprentice's plan.

### Manager dashboard

Should prioritize:

- apprentice distribution;
- active training-plan versions;
- mentor assignments;
- high-level training progress;
- administrative exceptions.

---

## 8. Reference-data import

Official/public training data should enter PSICAS through importers rather than manual production edits whenever practical.

Conceptual flow:

```text
Official public source
        ↓
Source adapter
        ↓
Raw import snapshot
        ↓
Validation / normalization
        ↓
Draft training-plan version
        ↓
Review
        ↓
Published reference version
```

Requirements:

- retain source URL and retrieval date;
- retain source identifiers where available;
- detect meaningful changes between imports;
- never silently overwrite a published version;
- allow a source to be replaced without coupling the domain model to one website format.

---

## 9. Languages

Target application languages:

- French;
- German;
- Italian;
- English for the application/portfolio where appropriate.

Official training-plan labels should preserve source-language content and translations when officially available.

---

## 10. Privacy

The public repository and public demo must contain only fictional identities.

No data from the historical private PSICAS repository is to be copied into V2 unless it is clearly generic, authored for reuse and free of personal/internal information.

Production-oriented design should separate personally identifiable user data from public reference data.

---

## 11. Out of scope for the first usable release

To keep the project deliverable, the following are not required for the first release:

- payroll or HR administration;
- attendance/time tracking;
- school grading systems;
- direct integration with government authentication systems;
- real employer/customer production data;
- mobile native applications;
- complex billing/subscription features.

---

## 12. Definition of the first usable release

PSICAS reaches its first meaningful release when a demo organization can:

1. create or import a profession and training-plan version;
2. create an apprentice and mentor;
3. enroll the apprentice in that plan version;
4. browse the complete competency structure;
5. create evaluations with history;
6. display useful apprentice and mentor dashboards;
7. run the core test suite and CI successfully from a clean checkout.
