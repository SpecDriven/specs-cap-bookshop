# Authorization

**Assigned:** Tom Fischer

## Why

Today the only protected thing is the `submitOrder` action, which requires an
authenticated user (specs/custom-logic/submit-order.feature.md). The
`AdminService` is open to anyone who can reach the server, and users are the
development mock users `alice` and `bob`
(specs/services/served-out-of-the-box.feature.md).

## Direction

- Require the `admin` role on `AdminService`
  (tasks/restrict-admin-service-to-admins.md) as the first step.
- Bind an identity provider (XSUAA or IAS) instead of mock users when the
  app is deployed, and keep the mock users for local development.
- Decide whether visitors need to log in to browse at all; the readme's use
  case says browsing is open to everyone.

## Guardrails

- Authorization is declared with `@requires` and `@restrict` annotations, not
  coded in handlers, wherever the annotation can express it.
