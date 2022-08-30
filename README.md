## POSTGRESQL CREATE

```docker
version: '3.1'
services:
  db:
    image: bitnami/postgresql
    restart: always
    container_name: db
    environment:
      BITMAMI_DEBUG: "false"
      POSTGRESQL_PORT_NUMBER: "5432"
      POSTGRESQL_VOLUME_DIR: "/bitnami/postgresql"
      PGDATA: "/bitnami/postgresql/data"
      POSTGRES_POSTGRES_PASSWORD: "postgres"
      POSTGRES_USER: "admin"
      POSTGRES_PASSWORD: "postgres@123"
      TZ: "Asia/Taipei"
      POSTGRESQL_ENABLE_LDAP: "no"
      POSTGRESQL_ENABLE_TLS: "no"
      POSTGRESQL_LOG_HOSTNAME: "false"
      POSTGRESQL_LOG_CONNECTIONS: "false"
      POSTGRESQL_PGAUDIT_LOG_CATALOG: "off"
      POSTGRESQL_CLIENT_MIN_MESSAGES: "error"
      POSTGRESQL_MAX_CONNECTIONS: '1000'
    ports:
      - 5432:5432
    volumes:
      - ./bitnami/postgresql:/bitnami/postgresql

```


## PGBOUNCER CREATE

```docker

version: '3.1'
services:
  bitnami_bouncer:
    image: bitnami/pgbouncer:1.17.0
    container_name: bitnami_bouncer
    ports:
      - 6432:6432
    environment: 
      POOL_MODE: session
      POSTGRESQL_HOST: db
      POSTGRESQL_PORT: "5432"
      POSTGRESQL_DATABASE: postgres
      SERVER_RESET_QUERY: DISCARD ALL
      MAX_CLIENT_CONN: "1000"
      POSTGRESQL_USERNAME: "postgres"
      POSTGRESQL_PASSWORD: "postgres"
      PGBOUNCER_PORT: "6432"
      PGBOUNCER_BIND_ADDRESS: 0.0.0.0
      PGBOUNCER_IGNORE_STARTUP_PARAMETERS: extra_float_digits
      PGBOUNCER_AUTH_TYPE: trust
      PGBOUNCER_SET_DATABASE_USER: yes
      PGBOUNCER_SET_DATABASE_PASSWORD: yes
```



## PGBOUNCER_ISSUR
---

pgbouncer 如果不設定 `POSTGRESQL_PASSWORD`，會出現下列錯誤

```bash
pgbouncer 08:05:38.17 
pgbouncer 08:05:38.19 Welcome to the Bitnami pgbouncer container
pgbouncer 08:05:38.21 Subscribe to project updates by watching https://github.com/bitnami/containers
pgbouncer 08:05:38.23 Submit issues and feature requests at https://github.com/bitnami/containers/issues
pgbouncer 08:05:38.25 
pgbouncer 08:05:38.41 INFO  ==> ** Starting PgBouncer setup **
pgbouncer 08:05:38.53 INFO  ==> Validating settings in PGBOUNCER_* env vars...
pgbouncer 08:05:38.60 ERROR ==> POSTGRESQL_PASSWORD must be set
```
* POSTGRESQL_PASSWORD must be set

parameter: extra_float_digits 錯誤

```bash
pgbouncer 08:34:52.22 INFO  ==> ** Starting PgBouncer **
2022-08-30 08:34:52.299 UTC [1] LOG kernel file descriptor limit: 1048576 (hard: 1048576); max_client_conn: 100, max expected fd use: 152
2022-08-30 08:34:52.311 UTC [1] LOG listening on 0.0.0.0:6432
2022-08-30 08:34:52.312 UTC [1] LOG listening on unix:/tmp/.s.PGSQL.6432
2022-08-30 08:34:52.313 UTC [1] LOG process up: PgBouncer 1.17.0, libevent 2.1.12-stable (epoll), adns: c-ares 1.17.1, tls: OpenSSL 1.1.1n  15 Mar 2022
2022-08-30 08:35:26.925 UTC [1] WARNING C-0x40000fbef0: (nodb)/(nouser)@172.18.0.1:62580 unsupported startup parameter: extra_float_digits=2
2022-08-30 08:35:26.926 UTC [1] LOG C-0x40000fbef0: (nodb)/(nouser)@172.18.0.1:62580 closing because: unsupported startup parameter: extra_float_digits (age=0s)
2022-08-30 08:35:26.926 UTC [1] WARNING C-0x40000fbef0: (nodb)/(nouser)@172.18.0.1:62580 pooler error: unsupported startup parameter: extra_float_digits
```

* 添加 `PGBOUNCER_IGNORE_STARTUP_PARAMETERS: extra_float_digits`

password authentication failed

```bash
pgbouncer 08:40:57.08 INFO  ==> ** Starting PgBouncer **
2022-08-30 08:40:57.162 UTC [1] LOG kernel file descriptor limit: 1048576 (hard: 1048576); max_client_conn: 100, max expected fd use: 152
2022-08-30 08:40:57.174 UTC [1] LOG listening on 0.0.0.0:6432
2022-08-30 08:40:57.175 UTC [1] LOG listening on unix:/tmp/.s.PGSQL.6432
2022-08-30 08:40:57.176 UTC [1] LOG process up: PgBouncer 1.17.0, libevent 2.1.12-stable (epoll), adns: c-ares 1.17.1, tls: OpenSSL 1.1.1n  15 Mar 2022
2022-08-30 08:41:01.606 UTC [1] LOG C-0x40000fbef0: postgres/postgres@172.18.0.1:62588 login attempt: db=postgres user=postgres tls=no
2022-08-30 08:41:01.638 UTC [1] LOG S-0x4000102da0: postgres/postgres@172.18.0.2:5432 new connection to server (from 172.18.0.3:58774)
2022-08-30 08:41:02.219 UTC [1] LOG C-0x40000fc120: postgres/postgres@172.18.0.1:62592 login attempt: db=postgres user=postgres tls=no
2022-08-30 08:41:02.221 UTC [1] LOG S-0x4000102fd0: postgres/postgres@172.18.0.2:5432 new connection to server (from 172.18.0.3:58778)
2022-08-30 08:41:57.173 UTC [1] LOG stats: 0 xacts/s, 0 queries/s, in 123 B/s, out 1002 B/s, xact 35708 us, query 35708 us, wait 16173 us
2022-08-30 08:41:58.336 UTC [1] LOG C-0x40000fbef0: postgres/postgres@172.18.0.1:62588 closing because: client close request (age=56s)
2022-08-30 08:41:58.391 UTC [1] LOG C-0x40000fc120: postgres/postgres@172.18.0.1:62592 closing because: client close request (age=56s)
2022-08-30 08:41:58.452 UTC [1] LOG C-0x40000fc120: (nodb)/(nouser)@172.18.0.1:62596 no such user: test_user
2022-08-30 08:41:58.452 UTC [1] LOG C-0x40000fc120: (nodb)/test_user@172.18.0.1:62596 login attempt: db=testdb user=test_user tls=no
2022-08-30 08:41:58.455 UTC [1] LOG C-0x40000fc120: (nodb)/test_user@172.18.0.1:62596 closing because: password authentication failed (age=0s)
2022-08-30 08:41:58.456 UTC [1] WARNING C-0x40000fc120: (nodb)/test_user@172.18.0.1:62596 pooler error: password authentication failed
2022-08-30 08:41:58.470 UTC [1] LOG C-0x40000fc120: (nodb)/(nouser)@172.18.0.1:62598 no such user: test_user
2022-08-30 08:41:58.471 UTC [1] LOG C-0x40000fc120: (nodb)/test_user@172.18.0.1:62598 login attempt: db=testdb user=test_user tls=no
2022-08-30 08:41:58.473 UTC [1] LOG C-0x40000fc120: (nodb)/test_user@172.18.0.1:62598 closing because: password authentication failed (age=0s)
2022-08-30 08:41:58.473 UTC [1] WARNING C-0x40000fc120: (nodb)/test_user@172.18.0.1:62598 pooler error: password authentication failed
```

MultiUser 解決方式

```docker
volumes:
  - ./pgbouncer/conf/:/bitnami/pgbouncer/conf/
```

修改`pgbouncer.ini`及`user_list`

目錄需修改權限 chown 1001:1001 ./conf

## POSTGRESQL COMMAND
---
```sql
create database testdb;
create user test_user with encrypted password 'test';
grant all privileges on database testdb to test_user;
```
