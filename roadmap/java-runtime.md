# CAP Java Runtime

**Assigned:** Mia Kowalski

## Why

The tutorial shows the discount handler in both Node.js and Java
(specs/custom-logic/discount-for-overstocked-books.feature.md) and leaves the
choice of runtime to the team. Every other specification in this repo is
runtime-neutral, and should stay that way.

## Direction

- Keep a Java variant of the sample (`cds add java`, `mvn cds:watch`) that
  passes the same scenarios as the Node.js variant.
- Port the `submitOrder` handler (specs/custom-logic/submit-order.feature.md)
  so both variants answer 401, 409 and success identically.
- Test links in the feature files should eventually point at both test suites.

## Guardrails

- No scenario may describe behaviour only one runtime has. If it does, it is
  a bug in one of the runtimes or a gap in the spec.
