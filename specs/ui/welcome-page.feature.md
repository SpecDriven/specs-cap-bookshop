# Welcome Page

**Assigned:** Raj Patel

Unless replaced by a custom `index.html` in the `app/` folder, CAP serves a
generic welcome page at the root of the server. It lists every served
endpoint, so the OData services can be explored from the browser, and links
to Fiori previews that give a first glimpse of Fiori UIs without writing one.

## A custom index.html replaces the generic page @v1 [published]

- **Given** a file `app/index.html` exists in the project
- **When** `http://localhost:4004` is opened
- **Then** that file is served instead of the generic welcome page

## Every served endpoint is listed @v1 [published]

- **Given** the bookshop services are mounted
- **When** `http://localhost:4004` is opened in a browser
- **Then** the page lists `/admin` with Authors, Books and Genres
- **And** `/browse` with Books
- **And** each entity links to its OData collection

## Fiori preview links open a generic list report @v1 [published]

- **Given** the welcome page lists an entity
- **When** its "Fiori preview" link is opened
- **Then** a generic Fiori elements list report shows the entity's data
- **And** search, value helps and Fiori draft work from the CDS annotations (roadmap/fiori-ui.md)
