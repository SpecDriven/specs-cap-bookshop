# Restrict the Admin Service to administrators

**Assigned:** Tom Fischer

The ready-made sample carries the line `annotate AdminService with
@requires:'admin';` commented out, "disabled for getting-started guide". Once
the walkthrough is done, anyone reaching the server can create, change and
delete books, authors and genres (specs/services/admin-service.feature.md).

- Enable the annotation and give `alice` the `admin` role in the mock users.
- Add scenarios: `bob` gets 403 on `/admin/Books`, `alice` gets 200.
- Check the `submitOrder` flow still works for `bob`, who is not an admin
  (specs/custom-logic/submit-order.feature.md).
