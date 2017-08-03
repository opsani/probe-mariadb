# probe-mariadb
The mariadb probe connects to mariadb/mysql component instances and lists their databases.  This probe can be used to verify a deployed service provides access to the mariadb/mysql API on the component's service network (the same network which is used to consume that service).

The mariadb probe supports the following actions:

* `service_up` (default) - connect to mariadb and list databases, retrying until success or the action times out.  Succeed if the mariadb service is up, even if the connect response is authentication failure or invalid database.
* `check_access` - connect to mariadb and list databases, retrying until success or the action times out.  Succeed if the connect and database list operations succeed.  This action can be used to verify access to a user/password protected mariadb service.

These actions support the following arguments:

* `port` - port number (default `3306`)
* `user` - user (default `None`)
* `password` - password (default `""` empty string)
* `database` - database (default `None`)
* `timeout` - operation timeout *per service instance*, in seconds (default `120`).  This is how long to keep retrying if the mariadb service does not respond.

## examples

Here are a few examples in the form of quality gates specified in a Skopos TED file (target environment descriptor).  Quality gates associate probe executions to one or more component images.  During application deployment Skopos executes the specified probes to assess components deployed with matching images.

```yaml
quality_gates:
    mariadb_test:
        images:
            - mariadb:*
        steps:

            # verify mariadb service is up (default action service_up)
            - probe: opsani/probe-mariadb:v1

            # verify mariadb access
            - probe:
                image: opsani/probe-mariadb:v1
                action: check_access
                label: "check mariadb access on alternate port with timeout"
                arguments: { port: 10000, timeout: 30 }
            - probe:
                image: opsani/probe-mariadb:v1
                action: check_access
                label: "check mariadb access with user/password/database"
                arguments:
                    user: "my_user"
                    password: "my_password"
                    database: "my_database"
```
