---
title: gpfdist
---

# gpfdist

Serves data files to or writes data files out from Apache Cloudberry segments.

## Synopsis

```shell
gpfdist [-d <directory>] [-p <http_port>] [-P <last_http_port>] [-l <log_file>]
   [-t <timeout>] [-S] [-w <time>] [-v | -V] [-s] [-m <max_length>]
   [--ssl <certificate_path> [--sslclean <wait_time>] ]
   [--compress] [--multi_thread <num_threads>]
   [-c <config.yml>]

gpfdist -? | --help 

gpfdist --version
```

## Description

`gpfdist` is Apache Cloudberry parallel file distribution program. It is used by readable external tables and `gpload` to serve external table files to all Apache Cloudberry segments in parallel. It is used by writable external tables to accept output streams from Apache Cloudberry segments in parallel and write them out to a file.

In order for `gpfdist` to be used by an external table, the `LOCATION` clause of the external table definition must specify the external table data using the `gpfdist://` protocol (see the Apache Cloudberry command `CREATE EXTERNAL TABLE`).

> **Note** If the `--ssl` option is specified to enable SSL security, create the external table with the `gpfdists://` protocol.

The benefit of using `gpfdist` is that you are guaranteed maximum parallelism while reading from or writing to external tables, thereby offering the best performance as well as easier administration of external tables.

For readable external tables, `gpfdist` parses and serves data files evenly to all the segment instances in the Apache Cloudberry system when users `SELECT` from the external table. For writable external tables, `gpfdist` accepts parallel output streams from the segments when users `INSERT` into the external table, and writes to an output file.

> **Note** When `gpfdist` reads data and encounters a data formatting error, the error message includes a row number indicating the location of the formatting error. `gpfdist` attempts to capture the row that contains the error. However, `gpfdist` might not capture the exact row for some formatting errors.

For readable external tables, if load files are compressed using `gzip` or `bzip2` (have a `.gz` or `.bz2` file extension), `gpfdist` uncompresses the data while loading the data (on the fly). For writable external tables, `gpfdist` compresses the data using `gzip` if the target file has a `.gz` extension.

> **Note** Compression is not supported for readable and writeable external tables when the `gpfdist` utility runs on Windows platforms.

When reading or writing data with the `gpfdist` or `gpfdists` protocol, Apache Cloudberry includes `X-GP-PROTO` in the HTTP request header to indicate that the request is from Apache Cloudberry. The utility rejects HTTP requests that do not include `X-GP-PROTO` in the request header.

Most likely, you will want to run `gpfdist` on your ETL machines rather than the hosts where Apache Cloudberry is installed. To install `gpfdist` on another host, simply copy the utility over to that host and add `gpfdist` to your `$PATH`.

> **Note** When using IPv6, always enclose the numeric IP address in brackets.

## Options

**`-d directory`**

The directory from which `gpfdist` will serve files for readable external tables or create output files for writable external tables. If not specified, defaults to the current directory.

**`-l log_file`**

The fully qualified path and log file name where standard output messages are to be logged.

**`-p http_port`**

The HTTP port on which `gpfdist` will serve files. Defaults to 8080.

**`-P last_http_port`**

The last port number in a range of HTTP port numbers (http_port to last_http_port, inclusive) on which `gpfdist` will attempt to serve files. `gpfdist` serves the files on the first port number in the range to which it successfully binds.

**`-t timeout`**

Sets the time allowed for Apache Cloudberry to establish a connection to a `gpfdist` process. Default is 5 seconds. Allowed values are 2 to 7200 seconds (2 hours). May need to be increased on systems with a lot of network traffic.

**`-m max_length`**

Sets the maximum allowed data row length in bytes. Default is 32768. Should be used when user data includes very wide rows (or when `line too long` error message occurs). Should not be used otherwise as it increases resource allocation. Valid range is 32K to 256MB. (The upper limit is 1MB on Windows systems.)

> **Note** Memory issues might occur if you specify a large maximum row length and run a large number of `gpfdist` concurrent connections. For example, setting this value to the maximum of 256MB with 96 concurrent `gpfdist` processes requires approximately 24GB of memory (`(96 + 1) x 246MB`).

**`-s`**

Enables simplified logging. When this option is specified, only messages with `WARN` level and higher are written to the `gpfdist` log file. `INFO` level messages are not written to the log file. If this option is not specified, all `gpfdist` messages are written to the log file.

You can specify this option to reduce the information written to the log file.

**`-S (use O_SYNC)`**

Opens the file for synchronous I/O with the `O_SYNC` flag. Any writes to the resulting file descriptor block `gpfdist` until the data is physically written to the underlying hardware.

**`-w time`**

Sets the number of seconds that Apache Cloudberry delays before closing a target file such as a named pipe. The default value is 0, no delay. The maximum value is 7200 seconds (2 hours).

For a Apache Cloudberry with multiple segments, there might be a delay between segments when writing data from different segments to the file. You can specify a time to wait before Apache Cloudberry closes the file to ensure all the data is written to the file.

**`--ssl certificate_path`**

Adds SSL encryption to data transferred with `gpfdist`. After running `gpfdist` with the `--ssl certificate_path` option, the only way to load data from this file server is with the `gpfdist://` protocol. For information on the `gpfdist://` protocol, see [Loading and Unloading Data](../data-loading/load-data-using-gpfdist.md).

The location specified in certificate_path must contain the following files:

- The server certificate file, `server.crt`
- The server private key file, `server.key`
- The trusted certificate authorities, `root.crt`

The root directory (`/`) cannot be specified as certificate_path.

**`--sslclean wait_time`**

When the utility is run with the `--ssl` option, sets the number of seconds that the utility delays before closing an SSL session and cleaning up the SSL resources after it completes writing data to or from a Apache Cloudberry segment. The default value is 0, no delay. The maximum value is 500 seconds. If the delay is increased, the transfer speed decreases.

In some cases, this error might occur when copying large amounts of data: `gpfdist server closed connection`. To avoid the error, you can add a delay, for example `--sslclean 5`.

**`--compress`**

Enable compression during data transfer. When specified, `gpfdist` utilizes the Zstandard (`zstd`) compression algorithm.

This option is not available on Windows platforms.

**`--multi_threads num_threads`**

Sets the maximum number of threads that `gpfdist` uses during data transfer, parallelizing the operation. When specified, `gpfdist` automatically compresses the data (also parallelized) before transferring.

`gpfdist` supports a maximum of 256 threads.

This option is not available on Windows platforms.

**`-c config.yaml`**

Specifies rules that `gpfdist` uses to select a transform to apply when loading or extracting data. The `gpfdist` configuration file is a YAML 1.1 document.

This option is not available on Windows platforms.

**`-v (verbose)`**

Verbose mode shows progress and status messages.

**`-V (very verbose)`**

Verbose mode shows all output messages generated by this utility.

**`-? (help)`**

Displays the online help.

**`--version`**

Displays the version of this utility.

## Notes

The server configuration parameter `verify_gpfdists_cert` controls whether SSL certificate authentication is enabled when Apache Cloudberry communicates with the `gpfdist` utility to either read data from or write data to an external data source. You can set the parameter value to `false` to deactivate authentication when testing the communication between the Apache Cloudberry external table and the `gpfdist` utility that is serving the external data. If the value is `false`, these SSL exceptions are ignored:

- The self-signed SSL certificate that is used by `gpfdist` is not trusted by Apache Cloudberry.
- The host name contained in the SSL certificate does not match the host name that is running `gpfdist`.

> **Caution** Deactivating SSL certificate authentication exposes a security risk by not validating the `gpfdists` SSL certificate.

You can set the server configuration parameter `gpfdist_retry_timeout` to control the time that Apache Cloudberry waits before returning an error when a `gpfdist` server does not respond while Apache Cloudberry is attempting to write data to `gpfdist`. The default is 300 seconds (5 minutes).

If the `gpfdist` utility hangs with no read or write activity occurring, you can generate a core dump the next time a hang occurs to help debug the issue. Set the environment variable `GPFDIST_WATCHDOG_TIMER` to the number of seconds of no activity to wait before `gpfdist` is forced to exit. When the environment variable is set and `gpfdist` hangs, the utility is stopped after the specified number of seconds, creates a core dump, and sends relevant information to the log file.

This example sets the environment variable on a Linux system so that `gpfdist` exits after 300 seconds (5 minutes) of no activity.

```shell
export GPFDIST_WATCHDOG_TIMER=300
```

When you enable compression, `gpfdist` transmits a larger amount of data while maintaining low network usage. Note that compression can be time-intensive, and may potentially reduce transmission speeds. When you utilize multi-threaded execution, the overall time required for compression may decrease, which facilitates faster data transmission while maintaining low network occupancy and high speed.

## Examples

To serve files from a specified directory using port 8081 (and start `gpfdist` in the background):

```shell
gpfdist -d /var/load_files -p 8081 &
```

To start `gpfdist` in the background and redirect output and errors to a log file:

```shell
gpfdist -d /var/load_files -p 8081 -l /home/gpadmin/log &
```

To enable multi-threaded data transfer (with implicit compression) using four threads, start `gpfdist` as follows:

```shell
gpfdist -d /var/load_files -p 8081 --multi_thread 4
```

To stop `gpfdist` when it is running in the background:

--First find its process id:

```shell
ps ax | grep gpfdist
```

--Then stop the process, for example:

```shell
kill 3456
```

## See also

[gpload](/docs/sys-utilities/gpload.md), `CREATE EXTERNAL TABLE`
