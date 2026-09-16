# Align the sample data with the tutorial

**Assigned:** Lena Schmidt

The tutorial's CSV files and the ready-made sample's disagree
(specs/domain-model/initial-data.feature.md):

- Genres are four rows keyed by small integers in the tutorial, and a whole
  hierarchy keyed by UUIDs in the sample.
- The sample spells the author "Edgar Allen Poe"; the tutorial and the
  author's name have "Allan". The sample's OData tests assert the misspelling.
- The tutorial's Books rows have no `descr`, `price` or `currency`; the
  sample's do.

Decide which is canonical for this repo, fix the other, and move the two
proposed data scenarios to published or drop them.
