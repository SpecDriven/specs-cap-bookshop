# Make generated IDs safe

**Assigned:** Tom Fischer

New authors and books get `max(ID) + 4` when created without an id
(specs/services/admin-service.feature.md, proposed scenario). The sample's
own comment says it: not safe. Two concurrent creates read the same maximum
and collide on the primary key.

- Switch Books and Authors to the `cuid` aspect, or to a database sequence.
- Keep the integer ids in the CSV seed data working, or migrate the seed data
  to UUIDs the way the Genres file already is.
- Update the proposed scenario to describe the chosen scheme, then publish it.
