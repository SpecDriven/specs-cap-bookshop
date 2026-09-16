# Vue.js Bookshop UI

**Assigned:** Raj Patel

Besides Fiori, CAP services can be consumed from any UI frontend using standard
AJAX requests. The ready-made sample ships a small Vue.js app in `app/vue`
that browses and orders books through OData requests to `CatalogService`
(services/catalog-service.feature.md). The tutorial links to it but does not
build it, so every scenario here is proposed.

## Books are listed with author, genre and price @v1 [proposed]

- **Given** the page has loaded
- **When** it requests `/browse/ListOfBooks?$expand=genre($select=name),currency($select=symbol)`
- **Then** the table shows one row per book with title, author, genre name and price with its currency symbol

## Clicking a row shows the book's details @v1 [proposed]

- **Given** the list is shown
- **When** the visitor clicks a row
- **Then** the page requests `/browse/Books/{ID}?$select=descr,stock,image`
- **And** shows the description, the cover image and "N in stock"

## Login shows the current user @v1 [proposed]

- **Given** the page is opened anonymously
- **When** the visitor presses Login
- **Then** the page posts to `/user/login` and shows the user's id and locale
- **And** pressing the `u` key hides the user info again

## Ordering from the details pane updates the stock @v1 [proposed]

- **Given** a book's details are shown with a quantity field defaulting to 1
- **When** the visitor submits an order
- **Then** the page posts `{ book, quantity }` to `/browse/submitOrder` (custom-logic/submit-order.feature.md)
- **And** on success shows "Successfully ordered N item(s)." and the new stock
- **And** on failure shows the server's error message in red

## Typing in the search box filters the list @v1 [proposed]

- **Given** the list is shown
- **When** the visitor types in the search box
- **Then** the list is reloaded with `$search=<text>` appended
- **And** clearing the box restores the full list
