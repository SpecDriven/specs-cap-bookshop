# Production Database

**Assigned:** Lena Schmidt

## Why

SQLite in memory is the inner-loop development database
(specs/domain-model/initial-data.feature.md): it makes turnaround instant but
it is not for productive use. The target in production is SAP HANA.

## Direction

- `cds add hana` and deploy the same domain model to an SAP HANA Cloud
  instance; the CSV seed data stays the development seed only.
- Verify the constraints that are pushed down to the database
  (specs/custom-logic/input-validation.feature.md) behave identically on HANA.
- Keep a persistent SQLite file (`cds deploy --to sqlite`) as a middle ground
  for local testing with data that survives restarts.

## Guardrails

- The domain model must not fork per database. Anything HANA-specific goes
  into annotations, not into `db/schema.cds`.
