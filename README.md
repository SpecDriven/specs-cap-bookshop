# CAP Bookshop Specifications

Specifications for the SAP Cloud Application Programming Model (CAP) bookshop
sample, as built step by step in
https://cap.cloud.sap/docs/get-started/bookshop.

Data dir for SpecDrivenApp.

- `specs/` — feature files, one per capability of the bookshop: domain model,
  initial data, services, querying, custom logic, UIs
- `roadmap/` — directions the tutorial points at but does not build
- `tasks/` — small follow-ups on the sample as it stands

Scenarios marked `[published]` describe behaviour the tutorial itself shows.
Scenarios marked `[proposed]` describe behaviour found only in the ready-made
sample (https://github.com/capire/bookshop) or its sibling in
https://github.com/SAP-samples/cloud-cap-samples/tree/main/bookshop, and are
up for discussion.

The Node.js implementation generated from these specs lives in
https://github.com/SpecDriven/bookshop-cap-js. Every scenario that has a test
links to it with a `[test: … ]` line pointing at that repo's `test/` folder.
Run them with `npm test` there.

`code-repo` in `project-settings.json` points at a checkout of that repo
(default `../bookshop-cap-js`) so the editor can offer the CDS entity fields.
