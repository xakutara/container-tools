# Container tools

- [Description](#description)
- [Tools](#tools)
  - [envconfig](#envconfig)
    - [Usage](#usage)
    - [Configuration](#configuration)
  - [log](#log)
    - [Usage](#usage-1)
    - [Configuration](#configuration-1)
  - [superd](#superd)
    - [Usage](#usage-2)
    - [Configuration](#configuration-2)
  - [wait-for](#wait-for)
    - [Usage](#usage-3)
- [License](#license)
- [Author](#author)

## Description
A collection of POSIX compatible shell scripts used for configuration and
process management and logging inside Linux containers.

All scripts have been tested with Busybox, Debian and Alpine Linux inside
of a Docker container environment, but should work with other container
environments and non-containerized Linux machines.

## Tools

### envconfig
[envconfig.sh](bin/envconfig.sh) is a wrapper script to write environment
variables in config files.  
Replaces placeholders and creates files, then starts the given command.  
Supports multiline variables, reading from file paths and base64 encoded data.

#### Usage

```sh
./envconfig.sh [-f config_file] [command] [args...]
```

#### Configuration
The default envconfig configuration file is `/usr/local/etc/envconfig.conf`.  
An alternate configuration file can be provided via `-f` option.  

Each line of the configuration for envconfig must have the following format:

```
VARIABLE_NAME /absolute/path/to/config/file
```

Each mapped variable will be `unset` before the command given to envconfig is
run, unless the variable name is prefixed with an exclamation mark:

```
!VARIABLE_NAME /absolute/path/to/config/file
```

Empty lines and lines starting with a hash (`#`) will be ignored.  
Multiple mappings of the same `VARIABLE_NAME` or path are possible.

Placeholders in config files must have the following format:

```
{{VARIABLE_NAME}}
```

Variable content can be provided from a file location, given the following:  
The file path must be provided in a variable with `_FILE` suffix.  
The file contents will then be used for the variable without the prefix.  
For example, the contents of a file at `$DATA_FILE` will be used as `$DATA`.

Variable content can be provided in base64 encoded form, given the following:  
The base64 data must be provided in a variable with `B64_` prefix.  
The decoded data will then be used for the variable without the prefix.  
For example, the content of `$B64_DATA` will be decoded and used as `$DATA`.

### log
[log.sh](bin/log.sh) executes the given command and logs the output.  
A datetime prefix is added in front of each output line.

#### Usage

```sh
./log.sh command [args...]
```

#### Configuration
The location of the log output can be defined
with the following environment variable:

```sh
LOGFILE="/dev/stdout"
```

The date output formatting can be defined
with the following environment variable:

```sh
DATECMD="date -u +%Y-%m-%dT%H:%M:%SZ"
```

### superd
[superd.sh](bin/superd.sh) is a supervisor daemon to manage long running
processes as a group.  
All remaining child processes are terminated as soon as one child exits.  
Written as entrypoint service for multi-process docker containers.

#### Usage

```sh
./superd.sh [config_file]
```

#### Configuration
The default superd configuration file is `/usr/local/etc/superd.conf`.  
An alternate configuration file can be provided as first argument.

Each line of the superd configuration file must have the following format:

```sh
command [args...]
```

Each command will be run by superd as a background process.  
If one command terminates, all commands will be terminated.  
Empty lines and lines starting with a hash (`#`) will be ignored.

### wait-for
[wait-for.sh](bin/wait-for.sh) is a script to wait for the given host(s) to be
available via TCP before executing a given command.  
It accepts a number of `host:port` combinations to connect to via
[netcat](https://en.wikipedia.org/wiki/Netcat).  
The command to execute after each host is reachable can be supplied after the
`--` argument.  
The default timeout of `10` seconds can be changed via `-t timeout` argument.

#### Usage

```sh
./wait.sh [-t timeout] host:port [host:port] [...] [-- command args...]
```

## License
Released under the [MIT license](https://opensource.org/licenses/MIT).

## Author
[Sebastian Tschan](https://blueimp.net/)
