# Input Validation

**Assigned:** Mia Kowalski

Most input validation is declared, not coded: `srv/admin-constraints.cds`
annotates `AdminService.Books` with `@mandatory`, `@assert` and
`@assert.range`, and the generic runtime enforces them on every create and
update. The constraints live in their own file, next to the service definition
they govern, so the service definition stays clean. Instead of loading data
into the application to check it, each constraint is evaluated by a single
query pushed down to the database.

## Author must exist @v1 [published]

- **Given** an administrator creates or updates a book through `/admin/Books`
- **When** `author_ID` refers to no existing author, for example 999
- **Then** the request is rejected with 400
- **And** the message is "Specified Author does not exist"

## Authors and genres are validated too @v1 [proposed]

- **Given** the ready-made sample's `srv/admin-constraints.cds`
- **When** an author is saved without a `name`, or with a `dateOfBirth` after the `dateOfDeath`
- **Then** the request is rejected with 400 and a message naming the problem
- **When** a genre is saved without a `name`, or with itself as `parent`
- **Then** the request is rejected with 400 and "A genre cannot be its own parent" for the latter

## Constraints are enforced by the generic runtime @v1 [published]

- **Given** the annotations in `srv/admin-constraints.cds` and no validation code
- **When** any create or update on `/admin/Books` arrives
- **Then** every constraint is checked before the data is written
- **And** a violated constraint answers with 400 and names the offending field
- **And** nothing is written when any constraint fails

## Genre is mandatory and must exist @v1 [published]

- **Given** an administrator creates a book
- **When** `genre_ID` is missing
- **Then** the request is rejected with 400 as a missing mandatory field
- **When** `genre_ID` is 99, which no genre has
- **Then** the request is rejected with 400 and "Specified Genre does not exist"

## Price must be between 1 and 111 @v1 [published]

- **Given** `price @assert.range: [1,111]`, both ends inclusive
- **When** an administrator creates a book with a price
- **Then** the request succeeds or fails as follows

| price  | result   |
| ------ | -------- |
| 0.99   | rejected |
| 1      | accepted |
| 55.50  | accepted |
| 111    | accepted |
| 111.01 | rejected |

## Stock must be positive @v1 [published]

- **Given** `stock @assert.range: [(0),_]`, an open lower bound of 0 and no upper bound
- **When** an administrator creates a book with a stock
- **Then** the request succeeds or fails as follows

| stock | result   |
| ----- | -------- |
| -1    | rejected |
| 0     | rejected |
| 1     | accepted |
| 555   | accepted |

## Title is mandatory @v1 [published]

- **Given** an administrator creates a book through `/admin/Books`
- **When** `title` is missing or empty
- **Then** the request is rejected with 400
- **And** the error names the field `title`
