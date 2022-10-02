# PostgreSQL

- create a user

  - create a postgres user and database for current session user

    ```shell
    $ sudo -u postgres createuser -s $USER
    ```

    alternative to:

    ```shell
    $ createdb $USER
    ```

  - with password prompt

    ```shell
    $ sudo -u postgres createuser <name> --pwprompt
    ```

    > use `-p` for shorthand syntax.

  - interactive way to create a user

    ```shell
    $ sudo -u postgres createuser --interactive
    ```

  - create a user inside psql and give superuser privilages

    ```shell
    $ psql
    ```

    ```postgres
    psql=# CREATE USER <name>;
    ```

    alternative to:

    ```postgres
    psql=# CREATE ROLE <name> WITH LOGIN;
    ```

    you can add options like:

    ```postgres
    CREATE ROLE <name> WITH <options> | PASSWORD <password> | SUPERUSER
    ```

    expample:

    ```postgres
    psql=# CREATE USER <name> SUPERUSER;
    ```

    alternative to:

    ```postgres
    psql=# CREATE ROLE <name> LOGIN SUPERUSER;
    ```

- alter user password

  ```postgres
  psql=# ALTER USER <name> WITH PASSWORD <password>;

  psql=# ALTER USER <name> WITH SUPERUSER;
  ```

- delete user

  ```postgres
  psql=# DELETE USER <name>;
  ```

## full options of creating user in both methods

---

- shell options way:
  ```shell
  createuser <option> <name>
  ```
- psql way:
  ```postgres
  CREATE USER <name> WITH <option>;
  ```

| Option Syntax                                | PSQL                      | Explanation                                                                                                                      |
| -------------------------------------------- | ------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| -s <br> --superuser                          | SUPERUSER                 | Add the superuser privilege.                                                                                                     |
| -d <br> --createdb                           | CREATEDB                  | Allows the user to create databases.                                                                                             |
| -r <br> --createrole                         | CREATEROLE                | Not allowed to create databases (default).                                                                                       |
| -i <br> --inherit                            | INHERIT                   | Automatically inherit the privileges of roles (default).                                                                         |
| -l <br> ----login                            | LOGIN                     | LOGIN Allows the user to log into a session with the role name (default).                                                        |
| --replication                                | REPLICATION               | Allows initiating streaming replication and activating/deactivating backup mode.                                                 |
| -P <br> --pwprompt                           | PASSWORD '<password>'     | Initiates password creation prompt or adds provided password to the user. Avoid using this option to create a passwordless user. |
| /                                            | PASSWORD NULL             | Specifically sets the password to null. Every password authentication fails for this user.                                       |
| -c <number> <br> --connection-limit=<number> | CONNECTION LIMIT <number> | Sets the maximum number of connections for user. Default is without limit.                                                       |

---

All above commands have CAPITAL alternative that reverses their feature like:

| Option Syntax          | PSQL        | Explanation                       |
| ---------------------- | ----------- | --------------------------------- |
| -S <br> --no-superuser | NOSUPERUSER | No superuser privilege (default). |

Examples:

```shell
$ sudo -u postgres createuser -d -r -e <name>
```

```postgres
psql=# CREATE USER <name> WITH CREATEROLE CREATEDB;
```

---

## common commands in psql:

- get a list of users: `\du`
- show databases: `\l`
- create database: `CREATE DATABASE <name>`
- connect to a db: `\c <db-name>`
- describe schema of database: `\d`
- describe schema of a table: `\d <table-nam>`
- drop database: `DROP DATABASE [IF EXISTS] <name>`
- drop table: `DROP TABLE [IF EXISTS] <name>`

> most of the commands have detailed version which we can get by adding `+` at end of command.
> exp: `\du+`
