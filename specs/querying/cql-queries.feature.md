# Querying with CQL

**Assigned:** Raj Patel

CDS Query Language (CQL) is a high-level query language, similar to SQL but
adapted to CDS concepts: path expressions follow associations and nested
projections expand them. The same queries run in `cds repl` against the
primary database, against the application services, and against remote
services in another process — the API is uniform, and services push queries
down to the database whenever they can.

## Nested projections follow to-many associations @v1 [published]

- **Given** `cds repl ./` has bootstrapped the app
- **When** this nested query runs

```js
await SELECT.from `Authors { ID, name, books { ID, title, genre.name as genre } }`
```

- **Then** each author comes with an array of their books, each with its genre name

| ID  | name              | books                                              |
| --- | ----------------- | -------------------------------------------------- |
| 101 | Emily Brontë      | 201 Wuthering Heights (Drama)                      |
| 107 | Charlotte Brontë  | 207 Jane Eyre (Drama)                              |
| 150 | Edgar Allan Poe   | 251 The Raven (Mystery), 252 Eleonora (Romance)    |
| 170 | Richard Carpenter | 271 Catweazle (Fantasy)                            |

[test: follows to-many associations with nested projections : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/consuming-services.test.js#L21 ]

## Path expressions reach across associations @v1 [published]

- **Given** `cds repl ./` has bootstrapped the app
- **When** this query runs

```js
await SELECT `ID, title, genre.name as genre` .from `Books`
```

- **Then** the genre name is read through the association in one query

| ID  | title             | genre   |
| --- | ----------------- | ------- |
| 201 | Wuthering Heights | Drama   |
| 207 | Jane Eyre         | Drama   |
| 251 | The Raven         | Mystery |
| 252 | Eleonora          | Romance |
| 271 | Catweazle         | Fantasy |

[test: reaches across associations with path expressions : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/consuming-services.test.js#L26 ]

## Queries filter and expand along associations @v1 [proposed]

- **Given** `AdminService` connected locally
- **When** authors are read with their @books and each book's @currency name and symbol, filtered by @name `like 'E%'`
- **Then** Emily Brontë comes with Wuthering Heights in British Pound (£)
- **And** Edgar Allan Poe comes with The Raven and Eleonora in US Dollar ($)
- **And** Charlotte Brontë and Richard Carpenter are not in the result

[test: allows reading from local services using cds.ql : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/consuming-services.test.js#L48 ]

## Remote services are queried as if they were local @v1 [published]

- **Given** `cds watch` serves the bookshop in one terminal and `cds repl` runs in another
- **When** `await cds.service.bindings` runs in the REPL
- **Then** the bindings from `~/.cds-services.json` list `AdminService` at `/admin` and `CatalogService` at `/browse` on `http://localhost:4004`
- **When** `cds.connect.to('CatalogService')` is used to send the same read as before
- **Then** the results are identical to the local run
- **And** the proxy translated the query into an OData request behind the scenes

## Services answer the same queries as the database @v1 [published]

- **Given** `const CatalogService = await cds.connect.to('CatalogService')` and likewise `AdminService`
- **When** `CatalogService.read` is sent the same projection

```js
await CatalogService.read `ID, title, genre` .from `Books`
```

- **Then** the result equals the database query with `genre.name as genre`
- **When** `AdminService.read` is sent the nested projection

```js
await AdminService.read `Authors { ID, name, books { ID, title, genre.name as genre } }`
```

- **Then** the result equals the nested database query
- **And** each query was delegated to the primary database unchanged

[test: answers the same queries through the services : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/consuming-services.test.js#L31 ]

## Targets may be names or reflected definitions @v1 [proposed]

- **Given** `AdminService` connected locally and its reflected entity `Authors`
- **When** the authors are read as `SELECT.from(Authors)`, `AdminService.read(Authors)`, `AdminService.read('Authors')`, or `AdminService.run(SELECT.from('Authors'))`
- **Then** every form returns the same rows

[test: supports targets as strings or reflected defs : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/consuming-services.test.js#L38 ]
