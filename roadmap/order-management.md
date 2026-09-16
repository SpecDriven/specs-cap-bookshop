# Order Management

**Assigned:** Tom Fischer

## Why

`submitOrder` reduces stock and forgets the order
(specs/custom-logic/submit-order.feature.md). The readme states plainly that
order management is out of scope for the sample, and the tutorial keeps it
that way. A shop that cannot list what a customer bought is not a shop.

## Direction

- An `Orders` entity with line items, owned by the ordering user, written in
  the same transaction that reduces the stock.
- Publish the `OrderedBook` event the cloud-cap-samples variant already emits
  so a separate service can pick orders up.
- A visitor's "my orders" view on the Catalog Service.

## Guardrails

- Stock reduction stays a single conditional update; adding an order record
  must not reintroduce a read-then-write race.
