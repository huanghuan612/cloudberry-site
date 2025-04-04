---
title: createdb
---

# createdb

Creates a new database.

## Synopsis

```shell
createdb [<connection-option> ...] [<option> ...] [<dbname> ['<description>']]

createdb -? | --help

createdb -V | --version
```

## Description

`createdb` creates a new database in a Apache Cloudberry system.

Normally, the database user who runs this command becomes the owner of the new database. However, a different owner can be specified via the `-O` option, if the executing user has appropriate privileges.

`createdb` is a wrapper around the SQL command [`CREATE DATABASE`](/docs/sql-stmts/create-database.md).

## Options

**`dbname`**

The name of the database to be created. The name must be unique among all other databases in the Apache Cloudberry system. If not specified, reads from the environment variable `PGDATABASE`, then `PGUSER` or defaults to the current system user.

**`description`**

A comment to be associated with the newly created database. Descriptions containing white space must be enclosed in quotes.

**`-D tablespace | --tablespace=TABLESPACE`**

Specifies the default tablespace for the database. (This name is processed as a double-quoted identifier.)

**`-e | --echo`**

Echo the commands that `createdb` generates and sends to the server.

**`-E encoding | --encoding=ENCODING`**

Character set encoding to use in the new database. Specify a string constant (such as `'UTF8'`), an integer encoding number, or `DEFAULT` to use the default encoding.

**`-l locale | --locale=LOCALE`**

Specifies the locale to be used in this database. This is equivalent to specifying both `--lc-collate` and `--lc-ctype`.

**`--lc-collate=LOCALE`**

Specifies the `LC_COLLATE` setting to be used in this database.

**`--lc-ctype=LOCALE`**

Specifies the `LC_CTYPE` setting to be used in this database.

**`-O owner | --owner=OWNER`**

The name of the database user who will own the new database. Defaults to the user running this command. (This name is processed as a double-quoted identifier.)

**`-T template | --template=TEMPLATE`**

The name of the template from which to create the new database. Defaults to `template1`. (This name is processed as a double-quoted identifier.)

**`-V | --version`**

Print the `createdb` version and exit.

**`-? | --help`**

Show help about `createdb` command line arguments, and exit.

The options `-D`, `-l`, `-E`, `-O`, and `-T` correspond to options of the underlying SQL command `CREATE DATABASE`; see [`CREATE DATABASE`](/docs/sql-stmts/create-database.md) for details.

### Connection options

**`-h host | --host=HOSTNAME`**

The host name of the machine on which the Apache Cloudberry coordinator server is running. If not specified, reads from the environment variable `PGHOST` or defaults to localhost.

**`-p port | --port=PORT`**

The TCP port on which the Apache Cloudberry coordinator server is listening for connections. If not specified, reads from the environment variable `PGPORT` or defaults to 5432.

**`-U username | --username=USERNAME`**

The database role name to connect as. If not specified, reads from the environment variable `PGUSER` or defaults to the current system role name.

**`-w | --no-password`**

Never issue a password prompt. If the server requires password authentication and a password is not available by other means such as a `.pgpass` file, the connection attempt will fail. This option can be useful in batch jobs and scripts where no user is present to enter a password.

**`-W | --password`**

Force a password prompt.

**`--maintenance-db=DBNAME`**

Specifies the name of the database to connect to when creating the new database. If not specified, the `postgres` database will be used; if that does not exist (or if it is the name of the new database being created), `template1` will be used.

## Examples

To create the database `test` using the default options:

```shell
createdb test
```

To create the database `demo` using the Apache Cloudberry coordinator on host `gpcoord`, port `54321`, using the `LATIN1` encoding scheme:

```shell
createdb -p 54321 -h gpcoord -E LATIN1 demo
```

## See also

[`CREATE DATABASE`](/docs/sql-stmts/create-database.md), [dropdb](/docs/sys-utilities/dropdb.md)
