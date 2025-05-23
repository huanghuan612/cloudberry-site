---
title: gpmemwatcher
---

# gpmemwatcher

Tracks the memory usage of each process in a Apache Cloudberry cluster.

## Synopsis

```shell
gpmemwatcher [-f | --host_file <hostfile>]   
        
gpmemwatcher --stop [-f | --host_file <hostfile>]  

gpmemwatcher --version

gpmemwatcher -h | --help
```

## Description

The `gpmemwatcher` utility is a daemon that runs on all servers of a Apache Cloudberry cluster. It tracks the memory usage of each process by collecting the output of the `ps` command every 60 seconds. It is a low impact process that only consumes 4 MB of memory. It will generate approximately 30 MB of data over a 24-hour period.

You may use this utility if Apache Cloudberry is reporting `Out of memory` errors and causing segments to go down or queries to fail. You collect the memory usage information of one or multiple servers within the Apache Cloudberry cluster with `gpmemwatcher` and then use [gpmemreport](/docs/sys-utilities/gpmemreport.md) to analyze the files collected.

## Options

**`-f | --host_file hostfile`**

Indicates the hostfile input file that lists the hosts from which the utility should collect memory usage information. The file must include the hostnames and a working directory that exists on each one of the hosts. For example:

```shell
cdw:/home/gpadmin/gpmemwatcher_dir/working
sdw1:/home/gpadmin/gpmemwatcher_dir/working
sdw2:/home/gpadmin/gpmemwatcher_dir/working
sdw3:/home/gpadmin/gpmemwatcher_dir/working
sdw4:/home/gpadmin/gpmemwatcher_dir/working
```

**`--stop`**

Stops all the `gpmemwatcher` processes, generates `.gz` data files in the current directory, and removes all the work files from all the hosts.

**`--version`**

Displays the version of this utility.

**`-h | --help`**

Displays the online help.

## Examples

**Example 1: Start the utility specifying the list of hosts from which to collect the information**

Create the file `/home/gpadmin/hostmap.txt` that contains the following:

```shell
cdw:/home/gpadmin/gpmemwatcher_dir/working
sdw1:/home/gpadmin/gpmemwatcher_dir/working
sdw2:/home/gpadmin/gpmemwatcher_dir/working
sdw3:/home/gpadmin/gpmemwatcher_dir/working
sdw4:/home/gpadmin/gpmemwatcher_dir/working
```

Make sure that the path `/home/gpadmin/gpmemwatcher_dir/working` exists on all hosts.

Start the utility:

```shell
$ gpmemwatcher -f /home/gpadmin/hostmap.txt
```

**Example 2: Stop utility and dump the resulting into a `.gz` file**

Stop the utility you started in Example 1:

```shell
$ gpmemwatcher -f /home/gpadmin/hostmap.txt --stop
```

The results `.gz` files will be dumped into the directory where you are running the command:

```shell
$ [gpadmin@gpdb-m]$ ls -thrl

-rw-rw-r--. 1 gpadmin gpadmin 2.8K Nov 19 15:17 cdw.ps.out.gz
-rw-rw-r--. 1 gpadmin gpadmin 2.8K Nov 19 15:17 sdw1.ps.out.gz
-rw-rw-r--. 1 gpadmin gpadmin 2.8K Nov 19 15:17 sdw2.ps.out.gz
-rw-rw-r--. 1 gpadmin gpadmin 2.8K Nov 19 15:17 sdw3.ps.out.gz
-rw-rw-r--. 1 gpadmin gpadmin 2.8K Nov 19 15:17 sdw4.ps.out.gz
```

## See also

[gpmemreport](/docs/sys-utilities/gpmemreport.md)
