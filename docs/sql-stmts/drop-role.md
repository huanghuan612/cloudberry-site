---
title: DROP ROLE
---

# DROP ROLE

Removes a database role.

## Synopsis

```sql
DROP ROLE [IF EXISTS] <name> [, ...]
```

## Description

`DROP ROLE` removes the specified role(s). To drop a superuser role, you must be a superuser yourself. To drop non-superuser roles, you must have `CREATE ROLE` privilege.

A role cannot be removed if it is still referenced in any database; an error will be raised if so. Before dropping the role, you must drop all the objects it owns (or reassign their ownership) and revoke any privileges the role has been granted on other objects. The [`REASSIGN OWNED`](/docs/sql-stmts/reassign-owned.md) and [`DROP OWNED`](/docs/sql-stmts/drop-owned.md) commands can be useful for this purpose.

However, it is not necessary to remove role memberships involving the role; `DROP ROLE` automatically revokes any memberships of the target role in other roles, and of other roles in the target role. The other roles are not dropped nor otherwise affected.

## Parameters

**`IF EXISTS`**

Do not throw an error if the role does not exist. A notice is issued in this case.

**`name`**

The name of the role to remove.

## Examples

Remove the roles named `sally` and `bob`:

```sql
DROP ROLE sally, bob;
```

## Compatibility

The SQL standard defines `DROP ROLE`, but it allows only one role to be dropped at a time, and it specifies different privilege requirements than Apache Cloudberry uses.

## See also

[`REASSIGN OWNED`](/docs/sql-stmts/reassign-owned.md), [`DROP OWNED`](/docs/sql-stmts/drop-owned.md), [`CREATE ROLE`](/docs/sql-stmts/create-role.md), [ALTER ROLE](/docs/sql-stmts/alter-role.md), [`SET ROLE`](/docs/sql-stmts/set-role.md)
