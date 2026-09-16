# Submit Order

**Assigned:** Mia Kowalski

`submitOrder(book, quantity)` is an unbound action added to
`CatalogService` with `extend service CatalogService with { … }`. It
requires an authenticated user. Custom actions have no generic provider, so an
`on` handler implements it: reduce the book's stock if enough is on hand,
otherwise fail. Actual order management is out of scope for the bookshop.

## An OrderedBook event is emitted @v1 [proposed]

- **Given** the cloud-cap-samples variant declares `event OrderedBook : { book; quantity; buyer }`
- **When** an order succeeds
- **Then** the service emits `OrderedBook` with the book, the quantity and the id of the ordering user
- **And** other services may subscribe to it

## Anonymous users cannot order @v1 [published]

- **Given** the action is annotated `@requires: 'authenticated-user'`
- **When** a client posts to `/browse/submitOrder` without an `Authorization` header
- **Then** the response is 401 Unauthorized
- **And** no stock changes

## Ordering reduces stock @v1 [published]

- **Given** book 201 has 12 in stock
- **When** `bob` posts `{ "book": 201, "quantity": 3 }` to `/browse/submitOrder`
- **Then** the request succeeds
- **And** `GET /admin/Books/201/stock/$value` returns 9

## Orders beyond stock are rejected @v1 [published]

- **Given** book 201 has 2 in stock after two orders of 5
- **When** `bob` orders 5 more of book 201
- **Then** the response is 409 Conflict
- **And** the message is "5 exceeds stock for book #201"
- **And** the stock stays at 2

[test: should reject out-of-stock orders : https://github.com/SAP-samples/cloud-cap-samples/blob/main/bookshop/test/custom-handlers.test.js#L7 ]

## Quantity must be at least one @v1 [proposed]

- **Given** the ready-made sample's handler
- **When** `quantity` is 0 or negative
- **Then** the response is 400 with "quantity has to be 1 or more"
- **And** no stock changes

## Stock can be ordered down to zero @v1 [published]

- **Given** book 201 has 12 in stock
- **When** `bob` sends the same order of 3 repeatedly
- **Then** the requests succeed until the stock is depleted

| order | stock before | result             | stock after |
| ----- | ------------ | ------------------ | ----------- |
| 1     | 12           | accepted           | 9           |
| 2     | 9            | accepted           | 6           |
| 3     | 6            | accepted           | 3           |
| 4     | 3            | accepted           | 0           |
| 5     | 0            | 409, exceeds stock | 0           |

## Stock check and update are one statement @v1 [published]

- **Given** the handler runs `UPDATE Books SET stock = stock - quantity WHERE stock >= quantity`
- **When** two orders for the last copies of a book arrive at the same time
- **Then** at most one of them succeeds
- **And** the stock never goes below zero

## The action returns the remaining stock @v1 [proposed]

- **Given** the cloud-cap-samples variant declares `returns { stock: Integer }`
- **When** `alice` orders 1 of book 251
- **Then** the response body carries the stock after the order, one less than before

[test: calls unbound actions - basic variant using srv.send : https://github.com/SAP-samples/cloud-cap-samples/blob/main/bookshop/test/consuming-actions.test.js#L30 ]

## Unknown books are reported @v1 [proposed]

- **Given** no book has the id 999
- **When** `bob` orders any quantity of book 999
- **Then** the response is 404 with "Book #999 doesn't exist"
