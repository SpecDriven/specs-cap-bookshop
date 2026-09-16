# Cover the declarative constraints with tests

**Assigned:** Mia Kowalski

None of the scenarios in specs/custom-logic/input-validation.feature.md link
to a test. The constraints are enforced by the generic runtime, which is
exactly why nobody thought to test them, and exactly why a typo in
`admin-constraints.cds` would go unnoticed.

- One `cds.test` file posting to `/admin/Books` with each table row from the
  price and stock scenarios, plus the missing title, unknown author and
  unknown genre cases.
- Link every scenario to its test with a `[test: … ]` line.
