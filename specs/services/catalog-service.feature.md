# Catalog Service

**Assigned:** Tom Fischer

`CatalogService`, served at `/browse` (`srv/cat-service.cds`), is for
visitors to browse and order books. It serves a denormalized, read-only view on
Books with `author` and `genre` flattened to their names, to simplify
browsing. Authors and Genres are not exposed, nor are the internal admin
details `createdBy` and `modifiedBy`. Ordering is the action described in
custom-logic/submit-order.feature.md; the discount note on overstocked books
is custom-logic/discount-for-overstocked-books.feature.md.

## A list view leaves out the description @v1 [proposed]

- **Given** the ready-made sample's `ListOfBooks` projection, which excludes `descr`
- **When** a client sends `GET /browse/ListOfBooks?$expand=genre($select=name),currency($select=symbol)`
- **Then** each book comes with its genre name and currency symbol but no description
- **And** book 251 shows genre "Mystery" and the currency symbol "$"

[test: serves ListOfBooks with the currency expanded : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/odata.test.js#L188 ]

## Author and genre are flattened to names @v1 [published]

- **Given** the seed data
- **When** a client sends `GET /browse/Books?$select=ID,title,genre`
- **Then** `genre` is the genre's name, not a nested object

| ID  | title             | genre   |
| --- | ----------------- | ------- |
| 201 | Wuthering Heights | Drama   |
| 207 | Jane Eyre         | Drama   |
| 251 | The Raven         | Mystery |
| 252 | Eleonora          | Romance |
| 271 | Catweazle         | Fantasy |

- **And** `author` is likewise the author's name

[test: flattens author and genre to names : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/odata.test.js#L111 ]

## Authors and Genres are not exposed @v1 [published]

- **Given** `CatalogService` exposes only `Books`
- **When** a client sends `GET /browse/Authors` or `GET /browse/Genres`
- **Then** the response is 404 Not Found
- **And** neither entity set appears in `/browse/$metadata`

[test: does not expose Authors and Genres : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/odata.test.js#L126 ]

## Books is read-only @v1 [published]

- **Given** the `Books` projection is annotated `@readonly`
- **When** a client sends `POST`, `PATCH`, `PUT` or `DELETE` against `/browse/Books`
- **Then** the request is rejected with 405 Method Not Allowed
- **And** the data is unchanged

[test: rejects writes to the read-only Books : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/odata.test.js#L134 ]

## Filter by author name @v1 [published]

- **Given** the seed data
- **When** a client sends `GET /browse/Books?$select=ID,title,author&$filter=contains(author,'Bro')`
- **Then** only the Brontë books are returned

| ID  | title             | author           |
| --- | ----------------- | ---------------- |
| 201 | Wuthering Heights | Emily Brontë     |
| 207 | Jane Eyre         | Charlotte Brontë |

[test: filters by author name : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/odata.test.js#L143 ]

## Internal admin fields are hidden @v1 [published]

- **Given** the projection excludes `createdBy` and `modifiedBy`
- **When** a client reads `/browse/Books` or `/browse/$metadata`
- **Then** neither field is present
- **And** `createdAt` and `modifiedAt` are still available

[test: hides createdBy and modifiedBy : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/odata.test.js#L151 ]

## Page with top and skip @v1 [published]

- **Given** the five seed books
- **When** a client sends `GET /browse/Books?$select=title&$top=3`
- **Then** it gets Wuthering Heights, Jane Eyre and The Raven
- **When** the client sends `GET /browse/Books?$select=title&$skip=3`
- **Then** it gets Eleonora and Catweazle

[test: supports $top/$skip paging : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/odata.test.js#L164 ]

## Search matches any text field @v1 [proposed]

- **Given** the seed data with descriptions
- **When** a client sends `GET /browse/Books?$search=Po&$select=title,author`
- **Then** every book whose title, author or description contains "Po", ignoring case, is returned
- **And** that includes the Poe books and the Brontë books whose descriptions mention publication

[test: supports $search in multiple fields : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/odata.test.js#L171 ]

## Select a subset of fields @v1 [published]

- **Given** the seed data
- **When** a client sends `GET /browse/Books?$select=ID,title`
- **Then** each of the five books comes with only `ID` and `title`

[test: supports $select : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/odata.test.js#L182 ]
