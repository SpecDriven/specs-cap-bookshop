# Discount for Overstocked Books

**Assigned:** Mia Kowalski

The first piece of programmatic custom logic: an after-READ handler on
`CatalogService.Books` appends a discount note to the @title of every book
with more than 111 in @stock. Only the response changes — the stored @title is
untouched. The handler is written in Node.js (`srv/cat-service.js`, next to
`srv/cat-service.cds`) or in Java (`CatalogServiceHandler.java`); the
behaviour is the same either way.

## Books without a stock value are left alone @v1 [published]

- **Given** a book whose @stock is null
- **When** it is read through `/browse/Books`
- **Then** its @title is returned unchanged

[test: leaves books without a stock value alone : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/custom-handlers.test.js#L43 ]

## Exactly 111 in stock is not discounted @v1 [published]

- **Given** a book with exactly 111 in @stock
- **When** it is read through `/browse/Books`
- **Then** its @title is unchanged
- **Given** the same book with 112 in @stock
- **When** it is read again
- **Then** its @title ends with " -- 11% discount!"

[test: does not discount exactly 111 in stock : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/custom-handlers.test.js#L36 ]

## Handlers can be written in Node.js or Java @v1 [published]

- **Given** `cds add nodejs` has been run
- **When** `srv/cat-service.js` exports a class extending `cds.ApplicationService` that registers `this.after('READ', 'Books', …)` in `init()`
- **Then** the handler runs after every read of `Books`
- **Given** instead `cds add java` has been run and the server starts with `mvn cds:watch`
- **When** a `@Component` class annotated `@ServiceName(CatalogService_.CDS_NAME)` implements `EventHandler` with an `@After(event = READ, entity = Books)` method
- **Then** the same discount note appears in the same responses

[test: is written as an after READ handler on Books : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/custom-handlers.test.js#L11 ]

## Overstocked books are flagged in the catalog @v1 [published]

- **Given** the seed data (domain-model/initial-data.feature.md)
- **When** a client sends `GET /browse/Books?$select=ID,title,stock`
- **Then** the two books with more than 111 in @stock carry the note in their @title

| ID  | stock | title                      |
| --- | ----- | -------------------------- |
| 201 | 12    | Wuthering Heights          |
| 207 | 11    | Jane Eyre                  |
| 251 | 333   | The Raven -- 11% discount! |
| 252 | 555   | Eleonora -- 11% discount!  |
| 271 | 22    | Catweazle                  |

[test: flags overstocked books in the catalog : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/custom-handlers.test.js#L18 ]

## The stored title is unchanged @v1 [published]

- **Given** The Raven shows the discount note in `/browse/Books`
- **When** an administrator reads `/admin/Books/251`
- **Then** the @title is "The Raven" with no note
- **And** the value in the database is "The Raven"

[test: leaves the stored title unchanged : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/custom-handlers.test.js#L29 ]
