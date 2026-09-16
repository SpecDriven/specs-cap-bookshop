# Books, Authors and Genres

**Assigned:** Lena Schmidt

The bookshop's domain model, captured in CDS in `db/schema.cds` under the
namespace `sap.capire.bookshop`: the entities Books, Authors and Genres and the
associations between them. The model stays focused on the domain — secondary
concerns such as input validation live in separate files that annotate these
entities (custom-logic/input-validation.feature.md).

## A book belongs to one author and one genre @v1 [published]

- **Given** the entity `Books`
- **Then** it has the key @Books.ID of type Integer
- **And** the localized strings @title and @descr
- **And** an Integer @stock, a Decimal @price and a @currency
- **And** a to-one association @Books.author to Authors
- **And** a to-one association @Books.genre to Genres

[test: gives a book one author and one genre : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/domain-model.test.js#L8 ]

## An author has many books @v1 [published]

- **Given** the entity `Authors` with the key @Authors.ID (Integer) and a @name
- **Then** its to-many association @books resolves to every book whose @Books.author is this author
- **And** the association is declared with the on-condition `books.author = $self`

[test: gives an author many books : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/domain-model.test.js#L23 ]

## Books and Authors carry managed fields @v1 [published]

- **Given** Books and Authors include the `managed` aspect from `@sap/cds/common`
- **When** a record is created or changed through a service
- **Then** `createdAt`, `createdBy`, `modifiedAt` and `modifiedBy` are filled in automatically

[test: fills in the managed fields : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/domain-model.test.js#L35 ]

## Currency comes from the common reuse types @v1 [published]

- **Given** `Currency` is imported from `@sap/cds/common`
- **Then** a book's @currency is an association to the common Currencies code list, keyed by `code`
- **And** the compiled Books table stores it in the column `currency_code`

[test: takes Currency from the common reuse types : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/domain-model.test.js#L42 ]

## Genres form a hierarchy @v1 [published]

- **Given** the entity `Genres` is a code list (`sap.common.CodeList`) with the key @Genres.ID (Integer) and a `name`
- **Then** a genre may point to a @parent genre
- **And** a top-level genre has no @parent

[test: lets genres form a hierarchy : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/domain-model.test.js#L51 ]

## The model can be inspected as CSN @v1 [published]

- **Given** the domain model in `db/schema.cds`
- **When** `cds compile db/schema.cds` runs
- **Then** the parsed model is printed as a CSN object
- **And** `--to json`, `--to yaml` and `--to sql` print the same model as JSON, YAML and SQL DDL

[test: can be inspected as CSN : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/domain-model.test.js#L61 ]

## The model compiles to SQL DDL @v1 [published]

- **Given** the domain model in `db/schema.cds`
- **When** `cds compile db/schema.cds --to sql` runs
- **Then** a table `sap_capire_bookshop_Books` is emitted with these columns

| column        | type                    |
| ------------- | ----------------------- |
| ID            | INTEGER, primary key    |
| title         | NVARCHAR(255)           |
| descr         | NVARCHAR(2000)          |
| stock         | INTEGER                 |
| price         | DECIMAL(9, 2)           |
| author_ID     | INTEGER                 |
| genre_ID      | foreign key to Genres   |
| currency_code | NVARCHAR(3)             |

- **And** the tables `sap_capire_bookshop_Authors` and `sap_capire_bookshop_Genres` alongside it

[test: compiles to SQL DDL : https://github.com/SpecDriven/bookshop-cap-js/blob/main/test/domain-model.test.js#L71 ]
