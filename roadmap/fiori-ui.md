# SAP Fiori UI

**Assigned:** Raj Patel

## Why

The tutorial stops at the generic Fiori preview (specs/ui/welcome-page.feature.md).
A real bookshop needs a browse-and-order app for visitors and a maintenance
app for administrators, and CAP's out-of-the-box Fiori support means most of
that is annotations, not code.

## Direction

- A Fiori elements list report and object page on `CatalogService.Books`,
  with `@UI` annotations for the columns and the details page.
- Value helps for author and genre on `AdminService.Books`, and Fiori draft so
  administrators can save half-finished records.
- Keep annotations in `app/` files that annotate the services, not in the
  service definitions themselves — the same separation as the constraints
  (specs/custom-logic/input-validation.feature.md).

## Guardrails

- The Vue.js app (specs/ui/vue-bookshop-ui.feature.md) stays as the example
  of a plain AJAX consumer; the Fiori app does not replace it.
