---
title: gpinitstandby
---

# gpinitstandby

Adds and/or initializes a standby coordinator host for a Apache Cloudberry system.

## Synopsis

```shell
gpinitstandby { -s <standby_hostname> [-P port] | -r | -n } [-a] [-q] 
    [-D] [-S <standby_data_directory>] [-l <logfile_directory>] 
    [--hba-hostnames <boolean>] 

gpinitstandby -v 

gpinitstandby -?
```

## Description

The `gpinitstandby` utility adds a backup, standby coordinator instance to your Apache Cloudberry system. If your system has an existing standby coordinator instance configured, use the `-r` option to remove it before adding the new standby coordinator instance.

Before running this utility, make sure that the Apache Cloudberry software is installed on the standby coordinator host and that you have exchanged SSH keys between the hosts. It is recommended that the coordinator port is set to the same port number on the coordinator host and the standby coordinator host.

This utility should be run on the currently active *primary* coordinator host.

The utility performs the following steps:

- Updates the Apache Cloudberry system catalog to remove the existing standby coordinator information (if the `-r` option is supplied)
- Updates the Apache Cloudberry system catalog to add the new standby coordinator instance information
- Edits the `pg_hba.conf` file of the Apache Cloudberry coordinator to allow access from the newly added standby coordinator
- Sets up the standby coordinator instance on the alternate coordinator host
- Starts the synchronization process

A backup, standby coordinator instance serves as a 'warm standby' in the event of the primary coordinator becoming non-operational. The standby coordinator is kept up to date by transaction log replication processes (the `walsender` and `walreceiver`), which run on the primary coordinator and standby coordinator hosts and keep the data between the primary and standby coordinator instances synchronized. If the primary coordinator fails, the log replication process is shut down, and the standby coordinator can be activated in its place by using the `gpactivatestandby` utility. Upon activation of the standby coordinator, the replicated logs are used to reconstruct the state of the coordinator instance at the time of the last successfully committed transaction.

The activated standby coordinator effectively becomes the Apache Cloudberry coordinator, accepting client connections on the coordinator port and performing normal coordinator operations such as SQL command processing and resource management.

> **Important** If the `gpinitstandby` utility previously failed to initialize the standby coordinator, you must delete the files in the standby coordinator data directory before running `gpinitstandby` again. The standby coordinator data directory is not cleaned up after an initialization failure because it contains log files that can help in determining the reason for the failure.

If an initialization failure occurs, a summary report file is generated in the standby host directory `/tmp`. The report file lists the directories on the standby host that require clean up.

## Options

**`-a (do not prompt)`**

Do not prompt the user for confirmation.

**`-D (debug)`**

Sets logging level to debug.

**`--hba-hostnames boolean`**

Optional. Controls whether this utility uses IP addresses or host names in the `pg_hba.conf` file when updating this file with addresses that can connect to Apache Cloudberry. When set to 0 -- the default value -- this utility uses IP addresses when updating this file. When set to 1, this utility uses host names when updating this file. For consistency, use the same value that was specified for `HBA_HOSTNAMES` when the Apache Cloudberry system was initialized. <!-- For information about how Apache Cloudberry resolves host names in the `pg_hba.conf` file, see [Configuring Client Authentication](../../admin_guide/client_auth.html). -->

**`-l logfile_directory`**

The directory to write the log file. Defaults to `~/gpAdminLogs`.

**`-n (restart standby coordinator)`**

Specify this option to start a Apache Cloudberry standby coordinator that has been configured but has stopped for some reason.

**`-P port`**

This option specifies the port that is used by the Apache Cloudberry standby coordinator. The default is the same port used by the active Apache Cloudberry coordinator.

If the Apache Cloudberry standby coordinator is on the same host as the active coordinator, the ports must be different. If the ports are the same for the active and standby coordinator and the host is the same, the utility returns an error.

**`-q (no screen output)`**

Run in quiet mode. Command output is not displayed on the screen, but is still written to the log file.

**`-r (remove standby coordinator)`**

Removes the currently configured standby coordinator instance from your Apache Cloudberry system.

**`-s standby_hostname`**

The host name of the standby coordinator host.

**`-S standby_data_directory`**

The data directory to use for a new standby coordinator. The default is the same directory used by the active coordinator.

If the standby coordinator is on the same host as the active coordinator, a different directory must be specified using this option.

**`-v (show utility version)`**

Displays the version, status, last updated date, and checksum of this utility.

**`-? (help)`**

Displays the online help.

## Examples

Add a standby coordinator instance to your Apache Cloudberry system and start the synchronization process:

```shell
gpinitstandby -s host09
```

Start an existing standby coordinator instance and synchronize the data with the current primary coordinator instance:

```shell
gpinitstandby -n
```

> **Note** Do not specify the -n and -s options in the same command.

Add a standby coordinator instance to your Apache Cloudberry system specifying a different port:

```shell
gpinitstandby -s myhost -P 2222
```

If you specify the same host name as the active Apache Cloudberry coordinator, you must also specify a different port number with the `-P` option and a standby data directory with the `-S` option.

Remove the existing standby coordinator from your Cloudberry system configuration:

```shell
gpinitstandby -r
```

## See also

[gpinitsystem](/docs/sys-utilities/gpinitsystem.md), [gpaddmirrors](/docs/sys-utilities/gpaddmirrors.md), [gpactivatestandby](/docs/sys-utilities/gpactivatestandby.md)
