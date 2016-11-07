# Ansible Role: Redis

[![Build Status](https://travis-ci.org/bartekrutkowski/ansible-redis.svg?branch=master)](https://travis-ci.org/bartekrutkowski/ansible-redis)

Installs [Redis](http://redis.io/) on RHEL/CentOS, Debian/Ubuntu and FreeBSD systems with secure defaults.

## Requirements

On RedHat-based distributions, this role requires the EPEL repository (you can simply add the role `geerlingguy.repo-epel` to ensure EPEL repo is available).

## Security enchancements

Redis security model leaves all the responsibility to the users and is very dangerous service, when exposed to the Internet without changing the default settings it is being shipped with. This role, beside automating Redis installation on Linux and FreeBSD systems, ensures that every Redis instance installed using this role is configured to be as secure and production ready, as possible, from the very beginning.

To achieve this, following things are changed, when compared to vanilla Redis installations provided by other roles:

- AUTH password is being set
- Potentially dangerous commands (like CONFIG, SAVE, DEBUG and others) are aliased with random prefix
- The redis.conf config file permissions are changed to be world non-readable
- The database and log directories permissions are changed to be world non-readable

## Role Variables

### Security related variables:

    redis_auth_password: "some-very-long-and-random-password-you-should-set"

The password string used for AUTH command. Set it to something long and random, to make possible bruteforce attacks harder.

    redis_alias_prefix: "leave-blank-to-disable-or-set-proper-random_prefix"

The prefix string used to alias dangerous commands that should be hidden from casual Redis users. Set it to something long and random, to make possible bruteforce attacks harder. When left as empty string, it will actually disable the commands entirely.

    redis_alias_commands: [ 'bgrewriteaof', 'bgsave', 'config', 'debug',
                            'del', 'flushall', 'flushdb', 'keys', 'pexpire',
                            'rename', 'save', 'shutdown', 'spop', 'srem' ]

List of potentially dangerous commands to be aliased. Depending on your needs and usage case, you might want to add or
remove the commands from this list, but leaving at least `config` command is strongly advised. If the `redis_alias_prefix` is an empty string, all of the commands on this list are going to be disabled.

### RHEL/CentOS related variables:

    redis_enablerepo: epel

The repository to use for Redis installation, used only on RHEL/CentOS.

### Generic Redis variables:

Available variables are listed below, along with default values (see `defaults/main.yml`):

    redis_port: 6379
    redis_bind_interface: 127.0.0.1

Port and interface on which Redis will listen. Set the interface to `0.0.0.0` to listen on all interfaces.

    redis_unixsocket: ''

If set, Redis will also listen on a local Unix socket.

    redis_timeout: 300

Close a connection after a client is idle `N` seconds. Set to `0` to disable timeout.

    redis_loglevel: "notice"
    redis_logfile: /var/log/redis/redis-server.log

Log level and log location (valid levels are `debug`, `verbose`, `notice`, and `warning`).

    redis_databases: 16

The number of Redis databases.

    # Set to an empty set to disable persistence (saving the DB to disk).
    redis_save:
      - 900 1
      - 300 10
      - 60 10000

Snapshotting configuration; setting values in this list will save the database to disk if the given number of seconds (e.g. `900`) and the given number of write operations (e.g. `1`) have occurred.

    redis_rdbcompression: "yes"
    redis_dbfilename: dump.rdb
    redis_dbdir: /var/lib/redis

Database compression and location configuration.

    redis_maxmemory: 0

Limit memory usage to the specified amount of bytes. Leave at 0 for unlimited.

    redis_maxmemory_policy: "noeviction"

The method to use to keep memory usage below the limit, if specified. See [Using Redis as an LRU cache](http://redis.io/topics/lru-cache).

    redis_maxmemory_samples: 5

Number of samples to use to approximate LRU. See [Using Redis as an LRU cache](http://redis.io/topics/lru-cache).

    redis_appendonly: "no"

The appendonly option, if enabled, affords better data durability guarantees, at the cost of slightly slower performance.

    redis_appendfsync: "everysec"

Valid values are `always` (slower, safest), `everysec` (happy medium), or `no` (let the filesystem flush data when it wants, most risky).

    # Add extra include files for local configuration/overrides.
    redis_includes: []

Add extra include file paths to this list to include more/localized Redis configuration.

The redis package name for installation via the system package manager. Defaults to `redis` on RHEL/CentOS, `redis-server` on Debian/Ubuntu and `redis` on FreeBSD.

    redis_package_name: "redis28u"

## Dependencies

None, except for the EPEL repository for RHEL/CentOS systems.

## Example Playbook

    - hosts: all
      roles:
        - { role: bartekrutkowski.redis }

## License

MIT / BSD

## Author Information

This role was created in 2016 by [Bartek Rutkowski](http://github.com/bartekrutkowski) as a fork of role by Jeff Geerling.
