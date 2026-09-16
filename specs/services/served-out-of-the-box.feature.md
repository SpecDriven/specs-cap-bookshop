# Served Out of the Box

**Assigned:** Raj Patel

Simple service definitions in CDS are all that is needed to serve
full-fledged OData services. Behind the scenes, generic providers parse OData
requests, translate them into SQL queries against the primary database, and
return the results as OData responses. `cds watch` keeps the server in step
with the files on disk throughout.

## Development mock users sign in with basic auth @v1 [published]

- **Given** the development server with mocked authentication
- **When** a request carries `Authorization: Basic alice:` (user `alice`, empty password)
- **Then** the request runs as the authenticated user `alice`
- **And** `bob` signs in the same way
- **And** a request without the header runs anonymously

## OData v4 metadata is served @v1 [published]

- **Given** `CatalogService` is mounted at `/browse`
- **When** a client sends `GET /browse/$metadata`
- **Then** the response is 200 with the header `odata-version: 4.0` and an XML body
- **And** the body declares the entity set `Books` of type `CatalogService.Books`

[test: serves $metadata documents in v4 : https://github.com/SAP-samples/cloud-cap-samples/blob/main/bookshop/test/odata.test.js#L7 ]

## Requests are sent from a REST client file @v1 [published]

- **Given** the file `test/requests.http`, which `cds add http` scaffolds
- **When** it holds a `GET` on `/browse/Books` with `$select` and `$filter`, and a `GET` on `/admin/Authors` with `$expand` and basic auth
- **Then** each request can be sent from the editor and answered by the running server

## Service definitions compile to EDMX @v1 [published]

- **Given** `srv/cat-service.cds`
- **When** `cds compile srv/cat-service.cds --to edmx` runs
- **Then** an OData EDMX metadata document is printed
- **And** it is the same document the server serves at `/browse/$metadata`

## Services are mounted at their declared paths @v1 [published]

- **Given** `AdminService @(path:'/admin')` and `CatalogService @(path:'/browse')`
- **When** `cds watch` loads the service definitions
- **Then** it logs each service with its endpoint and the file and line it was declared at

| service        | at        | decl                     |
| -------------- | --------- | ------------------------ |
| AdminService   | /admin    | srv/admin-service.cds:3  |
| CatalogService | /browse   | srv/cat-service.cds:3    |

- **And** the server listens on `http://localhost:4004`

## Services can be reached from the browser @v1 [published]

- **Given** the server is running
- **When** `http://localhost:4004/browse/Books?$select=ID,title,genre` is opened in a browser
- **Then** the five books are returned as an OData JSON collection
- **And** `http://localhost:4004/admin/Authors?$select=ID,name&$expand=books($select=ID,title)` returns the authors with their books

## The watcher waits for content and restarts on change @v1 [published]

- **Given** `cds watch` runs in an empty project
- **Then** it reports "No models found in db/,srv/,app/,app/*" and waits
- **When** `db/schema.cds` is saved
- **Then** it deploys the database and reports "No service definitions found in loaded models"
- **When** the service files are saved
- **Then** it restarts and serves them, and does so again on every later change
