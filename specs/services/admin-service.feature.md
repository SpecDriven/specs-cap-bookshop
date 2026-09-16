# Admin Service

**Assigned:** Tom Fischer

`AdminService`, served at `/admin` (`srv/admin-service.cds`), is for
administrators to maintain master data. It exposes Authors, Books and Genres
as-is from the domain model, allowing full CRUD access to all data. Nothing
is coded for it: the generic providers handle every request
(services/served-out-of-the-box.feature.md). Input validation is declared
separately (custom-logic/input-validation.feature.md).

## A single field is readable as a raw value @v1 [published]

- **Given** book 201 has 12 in stock
- **When** a client sends `GET /admin/Books/201/stock/$value`
- **Then** the response body is the bare value `12`

[test: supports $value requests : https://github.com/SAP-samples/cloud-cap-samples/blob/main/bookshop/test/odata.test.js#L74 ]

## Administrators create, read, update and delete without custom code @v1 [published]

- **Given** the three entity projections and no handler code
- **When** an administrator sends `POST`, `GET`, `PATCH` or `DELETE` requests to `/admin/Books`, `/admin/Authors` or `/admin/Genres`
- **Then** the generic providers translate each request into SQL against the primary database
- **And** the change is visible on the next read, including through the Catalog Service (services/catalog-service.feature.md)

## All three entities are exposed as-is @v1 [published]

- **Given** `AdminService`
- **Then** `Authors`, `Books` and `Genres` are projections on the domain entities of the same name
- **And** every field of the domain entity is exposed, the managed fields `createdBy` and `modifiedBy` included

## Authors expand to their books @v1 [published]

- **Given** the seed data (domain-model/initial-data.feature.md)
- **When** a client sends `GET /admin/Authors?$select=ID,name&$expand=books($select=ID,title)`
- **Then** each author comes with the books written by them

| ID  | name              | books                              |
| --- | ----------------- | ---------------------------------- |
| 101 | Emily Brontë      | 201 Wuthering Heights              |
| 107 | Charlotte Brontë  | 207 Jane Eyre                      |
| 150 | Edgar Allan Poe   | 251 The Raven, 252 Eleonora        |
| 170 | Richard Carpenter | 271 Catweazle                      |

[test: supports $expand : https://github.com/SAP-samples/cloud-cap-samples/blob/main/bookshop/test/odata.test.js#L59 ]

## New authors and books get generated IDs @v1 [proposed]

- **Given** the ready-made sample's `srv/admin-service.js`
- **When** an administrator creates an author or a book without an `ID`
- **Then** the record gets the current highest `ID` of that entity plus 4
- **And** a request that brings its own `ID` keeps it
- **And** this scheme is known to be unsafe under concurrent creates (tasks/make-generated-ids-safe.md)
