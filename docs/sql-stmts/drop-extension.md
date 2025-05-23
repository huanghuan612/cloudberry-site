---
title: DROP EXTENSION
---

# DROP EXTENSION

Removes an extension from a Apache Cloudberry.

## Synopsis

```sql
DROP EXTENSION [ IF EXISTS ] <name> [, ...] [ CASCADE | RESTRICT ]
```

## Description

`DROP EXTENSION` removes extensions from the database. Dropping an extension causes its component objects to be dropped as well.

:::info
The supporting extension files that were installed to create the extension (for example, the library and `.control` files) are not deleted. The files must be manually removed from the Apache Cloudberry hosts.
:::

You must own the extension to use `DROP EXTENSION`.

## Parameters

**`IF EXISTS`**

Do not throw an error if the extension does not exist. Apache Cloudberry issues a notice in this case.

**`name`**

The name of an installed extension.

**`CASCADE`**

Automatically drop objects that depend on the extension, and in turn all objects that depend on those objects. Refer to the PostgreSQL [Dependency Tracking](https://www.postgresql.org/docs/14/ddl-depend.html) documentation for more information.

:::tip
Before issuing a `DROP EXTENSION` with the `CASCADE` keyword, you should be aware of all object that depend on the extension to avoid unintended consequences.
:::

**`RESTRICT`**

Refuse to drop an extension if any objects depend on it (other than its own member objects and other extensions listed in the same `DROP` command). This is the default.

## Examples

To remove the extension `hstore` from the current database:

```sql
DROP EXTENSION hstore;
```

This command fails if any of the extension objects are in use in the database. For example, if a table is defined with columns of the `hstore` type. Add the `CASCADE` option to forcibly remove those dependent objects.

## Compatibility

`DROP EXTENSION` is a Apache Cloudberry extension.

## See also

[`CREATE EXTENSION`](/docs/sql-stmts/create-extension.md), [`ALTER EXTENSION`](/docs/sql-stmts/alter-extension.md)
