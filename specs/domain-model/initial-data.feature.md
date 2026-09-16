# Initial Data

**Assigned:** Lena Schmidt

Seed data for local development: CSV files in `db/data`, one per entity, named
`<namespace>-<Entity>.csv`. CAP detects them and fills the database with them
whenever it deploys the in-memory SQLite database. `cds add data` scaffolds the
files, and `cds add data --records 10` generates sample records.

## An in-memory database is deployed automatically @v1 [published]

- **Given** a domain model saved under `db/`
- **When** `cds watch` picks it up
- **Then** it connects to `sqlite { url: ':memory:' }`
- **And** reports `/> successfully deployed to in-memory database.`
- **And** the database is a development stand-in only — production targets SAP HANA (roadmap/production-database.md)

[test: deploys an in-memory database automatically : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/initial-data.test.js#L8 ]

## CSV files are loaded on every restart @v1 [published]

- **Given** the files `sap.capire.bookshop-Authors.csv`, `sap.capire.bookshop-Books.csv` and `sap.capire.bookshop-Genres.csv` in `db/data`
- **When** `cds watch` restarts the server
- **Then** the log lists `> init from db/data/<file>` once per file
- **And** every row of every file is in the database afterwards

[test: loads every CSV file on every restart : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/initial-data.test.js#L13 ]

## Five books are seeded @v1 [published]

- **Given** `sap.capire.bookshop-Books.csv`
- **When** the database is deployed
- **Then** these books exist

| ID  | title             | author_ID | genre_ID | stock |
| --- | ----------------- | --------- | -------- | ----- |
| 201 | Wuthering Heights | 101       | 11       | 12    |
| 207 | Jane Eyre         | 107       | 11       | 11    |
| 251 | The Raven         | 150       | 16       | 333   |
| 252 | Eleonora          | 150       | 15       | 555   |
| 271 | Catweazle         | 170       | 13       | 22    |

[test: seeds five books : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/initial-data.test.js#L27 ]

## Foreign keys are given as `<association>_ID` columns @v1 [published]

- **Given** `Books.csv` has the columns `author_ID` and `genre_ID`
- **When** book 251 is loaded with `author_ID` 150 and `genre_ID` 16
- **Then** its author resolves to Edgar Allan Poe
- **And** its genre resolves to Mystery

[test: resolves foreign keys given as association_ID columns : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/initial-data.test.js#L38 ]

## Foreign keys are given as `<association>_ID` columns @v2 [proposed]

- **Given** `Books.csv` has the columns `author_ID` and `genre_ID`
- **When** book 251 is loaded with `author_ID` 150 and `genre_ID` 16
- **Then** its @Books.author resolves to Edgar Allan Poe
- **And** its @Books.genre resolves to Mystery

[test: resolves foreign keys given as association_ID columns : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/initial-data.test.js#L38 ]

## Four authors are seeded @v1 [published]

- **Given** `sap.capire.bookshop-Authors.csv`
- **When** the database is deployed
- **Then** these authors exist

| ID  | name              |
| --- | ----------------- |
| 101 | Emily Brontë      |
| 107 | Charlotte Brontë  |
| 150 | Edgar Allan Poe   |
| 170 | Richard Carpenter |

[test: seeds four authors : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/initial-data.test.js#L43 ]

## Four genres are seeded @v1 [published]

- **Given** `sap.capire.bookshop-Genres.csv`
- **When** the database is deployed
- **Then** these genres exist

| ID | name    |
| -- | ------- |
| 11 | Drama   |
| 13 | Fantasy |
| 15 | Romance |
| 16 | Mystery |

[test: seeds four genres : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/initial-data.test.js#L53 ]

## Genres are a full hierarchy in the ready-made sample @v1 [proposed]

- **Given** the sample's `sap.capire.bookshop-Genres.csv` keyed by UUID
- **When** the database is deployed
- **Then** Fiction and Non-Fiction are the roots
- **And** Drama, Poetry, Fantasy, Science Fiction, Romance, Mystery, Adventure, Short Story and Graphic Novel hang under Fiction
- **And** deeper levels exist, for example Mystery → Crime → Thriller → Spy Thriller

## Translated titles are seeded from a texts file @v1 [proposed]

- **Given** `sap.capire.bookshop-Books.texts.csv` with `locale`, @title and @descr per book
- **When** a client reads book 201 with `Accept-Language: de`
- **Then** its @title is "Sturmhöhe"
- **And** a client without a matching locale still gets "Wuthering Heights"

[test: seeds translated titles from a texts file : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/initial-data.test.js#L63 ]
