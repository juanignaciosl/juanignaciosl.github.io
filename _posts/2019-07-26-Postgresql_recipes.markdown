---
layout: post
comments: true
title: 'PostgreSQL recipes'
date: 2019-07-26 07:18:57
categories: ['development']
tags: [Software Development, PostgreSQL, tools]
excerpt: |
    Both from a gist with notes and multiple bookmarks and pending Pocket'd entries, I've collected a lot
    of useful stuff about PostgreSQL. Don't expect a lot of order, though :-)
image:
  feature: computer_1388x800.jpg
---

# psql

I use IntelliJ support for databases more and more, especially if large queries are involved. It also provides
assistance within code (validation and autocompletion of table or columns names at SQL strings within code, for
example), which is nice. Nevertheless, `psql` is still my go-to tool for PostgreSQL.

Please note that most `\d` commands support additional param of `__schema__.name__` and accept wildcards like `*.*`.

## .psqlrc

There are plenty of things that you can do with a `~/.psqlrc` file. In general, every psql command can be added there
and will be run at the beginning of your `psql` session. For example, you can create shortcuts to queries by adding
 this to `~/.psqlrc`:

```sql
\set total_index_size 'SELECT pg_size_pretty(sum(relpages*1024)) AS size FROM pg_class WHERE reltype=0;'
```

Then you can run it with `:total_index_size;`.

## Working with external files and commands

- Running external queries (I use [this](https://github.com/juanignaciosl/snippets/blob/master/pg/locks.sql)
a lot for [lock detection](https://wiki.postgresql.org/wiki/Lock_Monitoring)): `\i ~/Development/snippets/pg/locks.sql`

- Run external command: `\!`.
- Output to file: `\o`. Example:

```
db=# \o a.txt
db=# EXPLAIN SELECT * FROM users WHERE id IN (SELECT user_id FROM groups WHERE name = 'admins');
db=# \o b.txt
db=# EXPLAIN SELECT users.* FROM users LEFT JOIN groups WHERE groups.name = 'admins';
db=# \! vimdiff a.txt b.txt
```

## Output formatting

_Some of the next hints are especially useful if you use `\o` to redirect the output to a file._

- `\x`: toggle between columns (default) or rows output (useful for single row searches). You can also set `\x auto` and
psql will do its best.
- `\a`: toggle column alignment.

## Environment variables

You can manipulate them with `\set` and `\unset`:

- `ECHO_HIDDEN`: enables output of catalog queries of `psql` commands. If you enable it you can, for example, get
to know what query `\df` is based on (very useful to learn about the catalog or narrow searches). You can also enable
it by running `psql` with `-E`.

## Configuration parameters

You can see them with `SHOW`:

- `data_directory`.
- `statement_timeout`.

## Editing

* `\ef`: edit function.

## Catalog

* `\l` / `\list` lists all databases.
* `\dn`: list schemas.
* `\dt` lists all tables in the current database.
* `\d __table__`: show table definition including triggers.
* `\df` list functions and its code.
* `\df+` list functions and its code. `\x` before pretty-formats it.

* `\du`: list roles.
- `\drds <username>`: show configuration, including `search_path`.

## Tag queries

Just like bash, you can use CTRL+R to search previous queries. If you add a comment after the semicolon, you can use it
for searching.

Anyway, relying on history is a source of pain. While this trick is cool, I suggest using external files or IntelliJ
scratch files.

## ~/.psqlrc

You can create a `.psqlrc` file with your desired configuration (such as `\set HISTSIZE 10000`).

# Catalog tables and views

Not a comprehensive list, just the ones that I use the most.
[This is a good article if you want more](http://www.alberton.info/postgresql_meta_info.html#.VKkAV2SG8kx).

- `information_schema.schemata`: schema information.
- `information_schema.role_table_grants`: table privileges.
- `pg_available_extensions`.
- `pg_proc`: procedure/functions.
- `pg_roles`.
- `pg_views`.

Show table indexes:
```sql
SELECT *
FROM pg_indexes
WHERE tablename='__table_name__' AND schemaname='__schema_name__'
```

Get all indexes from all tables of a schema:

```sql
SELECT
   t.relname AS table_name,
   i.relname AS index_name,
   a.attname AS column_name
FROM
   pg_class t,
   pg_class i,
   pg_index ix,
   pg_attribute a,
   pg_namespace n
WHERE
   t.oid = ix.indrelid
   AND i.oid = ix.indexrelid
   AND a.attrelid = t.oid
   AND a.attnum = ANY(ix.indkey)
   AND t.relnamespace = n.oid
   AND n.nspname = 'kartones'
ORDER BY
   t.relname,
   i.relname
```

## (Prettified) sizes

Show DB table space in use:
```sql
SELECT pg_size_pretty(pg_total_relation_size('__table_name__'))
```

Show DB space in use:
```sql
SELECT pg_size_pretty(pg_database_size('__database_name__'))
```

## Object dependencies

```sql
select * from pg_shdepend where refobjid = 450809
```

## Roles a user/role has

```sql
WITH RECURSIVE cte AS (
   SELECT oid FROM pg_roles WHERE rolname = 'maxwell'

   UNION ALL
   SELECT m.roleid
   FROM   cte
   JOIN   pg_auth_members m ON m.member = cte.oid
)
SELECT oid FROM cte
```

## Ownerships

```sql
select nsp.nspname as object_schema,       
cls.relname as object_name,
       rol.rolname as owner,
       case cls.relkind
         when 'r' then 'TABLE'
         when 'i' then 'INDEX'
         when 'S' then 'SEQUENCE'
         when 'v' then 'VIEW'
         when 'c' then 'TYPE'
         else cls.relkind::text
       end as object_type
from pg_class cls
  join pg_roles rol on rol.oid = cls.relowner
  join pg_namespace nsp on nsp.oid = cls.relnamespace
where nsp.nspname not in ('information_schema', 'pg_catalog')
  and nsp.nspname not like 'pg_toast%'
and rol.rolname = 'username'
order by nsp.nspname, cls.relname
```

# Storage parameters

See [docs about runtime config autovacuum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html)
specially `autovacuum_analyze_threshold`.

```sql
SELECT c.reloptions
FROM pg_class c 
INNER JOIN pg_namespace n ON c.relnamespace = n.oid
WHERE c.relname = 'visualizations' AND n.nspname = 'public';
```



# Monitoring

- Enable `log_min_duration_statement=0` in conf file.

## `pg_stat_activity`

### Some queries

```sql
SELECT * FROM pg_stat_activity
```

```sql
SELECT datid, datname, pid, usesysid, usename, application_name, client_addr, client_hostname, client_port,
    backend_start, xact_start, query_start, state_change, waiting, state, query
FROM pg_stat_activity
ORDER BY query_start DESC
```

```sql
SELECT datid, datname, pid, usesysid, usename, application_name, client_addr, waiting, state, query
FROM pg_stat_activity
ORDER BY query_start DESC
```

```sql
SELECT datname, application_name, client_addr, waiting, state, query, query_start
FROM pg_stat_activity
ORDER BY query_start DESC
```

```sql
SELECT
  pid,
  now() - pg_stat_activity.query_start AS duration,
  query,
  state
FROM pg_stat_activity
WHERE state <> 'idle'
  AND now() - pg_stat_activity.query_start > interval '5 minutes'
ORDER BY pg_stat_activity.query_start
```

- `SELECT pg_cancel_backend(__pid__);`: terminates an idle query.
- `SELECT pg_terminate_backend(__pid__);`: terminates an idle connection.

Close all connections but self:

```sql
SELECT pg_terminate_backend(pg_stat_activity.pid)
FROM pg_stat_activity
WHERE pg_stat_activity.datname = 'TARGET_DB'
  AND pid <> pg_backend_pid()
```

Show queries being executed at a certain DB. Can also display query time, etc:
```sql
SELECT pid, datname, waiting, state, query FROM pg_stat_activity WHERE datname='__database_name__'
```

Get all queries waiting for data (that might be hung):
```sql
SELECT * FROM pg_stat_activity WHERE waiting='t'
```

Get all currently executing queries:
```sql
SELECT pg_stat_get_backend_pid(s.backendid) AS procpid, 
  pg_stat_get_backend_activity(s.backendid) AS current_query
FROM (SELECT pg_stat_get_backend_idset() AS backendid) AS s;
```

## Logging


Changing verbosity & querying Postgres log:
- First edit the config file, set a decent verbosity, save and restart postgres:

```bash
$ sudo vim /etc/postgresql/9.3/main/postgresql.conf
```

Then uncomment/change inside:
```
log_min_messages = debug2
log_min_error_statement = debug2
```

```bash
$ sudo service postgresql restart
```
- Now you will get tons of details of every statement, error, and even background tasks like VACUUMs
```
tail -f /var/log/postgresql/postgresql-9.3-main.log
```
- How to add user who executed a PG statement to log (editing `postgresql.conf`):
```
log_line_prefix = '%t %u %d %a '
```

In order to enable debug level for PL/Python (and probably other scripting languages) you must enable debug2 at client_min_messages / log_min_messages . debug2 seems to be the equivalent for the old global debug level

# Exports and dumps

## Basic CSV export/import

```sql
\copy (SELECT ...) TO export.csv WITH (FORMAT csv, HEADER true)
```

```sql
\copy users from /tmp/users.171109.csv with (format csv, header true);
```

You can specify columns after table name (`\copy users (id, username) ...`) if the columns of the file don't match the ones at the table (or its order).

## Export specific data

```sql
create table zz_correction_items_export as
select * from correction_items where id = 'be102ada-bb99-41ae-93a2-b1d7e6345977';

create table zz_corrections_export as
select * from corrections where id = 'fcfe65ad-4ec6-4571-b323-8ec06191c2a5';
```

```bash
$ pg_dump --table=zz_correction_items_export --data-only --column-inserts idiomcoach > tmp/zz_correction_items_export.sql
$ pg_dump --table=zz_corrections_export --data-only --column-inserts idiomcoach > tmp/zz_corrections_export.sql
```

## Export/import schema

```bash
$ pg_dump -U postgres -t 'public.users' --schema-only cartodb_central_development
```

Export/import:

```bash
$ pg_dump -U postgres -F c -t 'public.users' --schema-only cartodb_central_development | pg_restore -U postgres -d temp
```

Faster restore:

```bash
pg_restore -U postgres -j4 -O -x --verbose -e -d DB_NAME DUMP_LOCATION`
```

Restore from text dump:
```bash
cat mc_zcta5 | psql -U postgres -d gis -v ON_ERROR_STOP=1`.
```

# Database copy or backup

## From one DB to another

```bash
$ createdb -T app_db app_db_backup
$ dropdb app_db
$ createdb -T app_db_backup app_db
$ pg_dump idiomcoach -p 5432 | psql idiomcoach -p 5433 # Copy from one to another.
```
  
## File dump

[Docs](https://www.postgresql.org/docs/current/static/backup-dump.html)

```bash
$ pg_dump dbname > outfile
...
$ psql dbname < infile
```

### Partial dumps

#### With copy to / from

```bash
$ psql -U postgres gis --port 5555 --host 0.0.0.0 -Atc "copy (select * from \"au.wadus\".mc_mesh_block where month in ('07/01/2018','08/01/2018','09/01/2018', '10/01/2018')) to stdout" > ./mc_au_mesh_block.dump
$ psql -U postgres gis --port 5555 --host 0.0.0.0 -Atc "copy (select * from \"au.wadus\".mc_mesh_block where month in ('07/01/2018','08/01/2018','09/01/2018', '10/01/2018')) to stdout" > ./mc_au_mesh_block.dump

$ cat /tmp/xyz_uk_do_geoms_12_13.dump | psql -U postgres gis --port 5555 --host 0.0.0.0 -Atc "copy tiler.xyz_uk_do_geoms from stdin"
```



# SQL

Not (all) specifically for PostgreSQL, but useful anyway:

Casting:
- `CAST (column AS type)` or `column::type`
- `'__table_name__'::regclass::oid`: get oid having a table name.

## Creating test data

```sql
CREATE TABLE p
(
    p_id serial PRIMARY KEY,
    p_name character varying UNIQUE
);

INSERT INTO p (p_name)
    SELECT substr(gen_salt('md5'), 4)
    FROM generate_series(1, 1000000);
Output

|  p_id     |  p_name    |
--------------------------
|  1        |  5aHN4w0f  |
|  2        |  29LKwrBw  |
|  3        |  9cIR4iXE  |
|  4        |  5P9aTUQN  |
|  ...      |  ...       |
|  1000000  |  6cpGNL18  |
Pretty random looking. Now for the orders table.

CREATE TABLE o
(
    o_id serial PRIMARY KEY,
    p_id integer REFERENCS p (p_id)
);

INSERT INTO o (p_id)
SELECT rnd_val
FROM (SELECT trunc(random() * 249999 + 1)::int AS rnd_val
        FROM generate_series(1, 1000000)) as gen;
Output

|  o_id     |  p_id    |
------------------------
|  1        |  1       |
|  2        |  525     |
|  3        |  759     |
|  4        |  1       |
|  ...      |  ...     |
|  1000000  |  225001  |
```

## Date ranges

Given a date range column (`tstzrange`, for example)', this is the expression to check overlapping:
`SELECT ... WHERE [2019-06-30T22:00:00.000Z, 2019-07-01T22:00:00.000Z]' && "my_date_range_column"`.

Check other [range functions and operators](https://www.postgresql.org/docs/11/functions-range.html).

# Window functions use cases

## Detecting duplicates

[Source](http://jakeyesbeck.com/2016/02/21/four-postgresql-tips/?utm_source=postgresweekly&utm_medium=email).

```sql
SELECT id from (
  SELECT id,
  ROW_NUMBER() OVER(
    PARTITION BY first_name,
                 last_name,
                 email
              ORDER BY id
    ) AS user_row_number
  FROM users
) duplicates
WHERE
duplicates.user_row_number > 1
```


# Optimizing

Some random notes, go to reference for more:

1. Create indexes.
  1. Consider `text_pattern_ops` for text columns if you're going to use `like`.
2. Cluster the tables.
3. Partition.

# References

You'll see that many sources are repeated, because there are many companies out there doing a great job on top of
PostgreSQL. Subscribe to Citus Data, 2nd Quadrant and Several Nines if you are interested in PostgreSQL.

Please subscribe to [Postgres Weekly](https://postgresweekly.com/) as well.

What I love about the following links is not only the direct content but the side knowledge that they explain. For
example, if you're not building a queue you might not be interested in a `SKIP LOCKED` post, but the introduction about
the caveats of some wrong approach is anyway great.

Tips:
- Shameless plug: my [PostgreSQL: The one tool to bring to a desert island](http://0.0.0.0:4000/postgresql/2017/12/14/postgresql-the-one-tool.html) talk (I'm available for repeating updated versions of it :) ).
- [Don't do this](https://wiki.postgresql.org/wiki/Don%27t_Do_This).
- [Postgres tips for the average and power user](https://www.citusdata.com/blog/2019/07/17/postgres-tips-for-average-and-power-user/).
- [Lessons learned scaling PostgreSQL database to 1.2bn records/month](https://medium.com/@gajus/lessons-learned-scaling-postgresql-database-to-1-2bn-records-month-edc5449b3067), great one with a lot of hands-on wisdom.
- [On Rocks and Sand](https://www.2ndquadrant.com/en/blog/on-rocks-and-sand/?source=post_page---------------------------).
- [My Favorite PostgreSQL Queries and Why They Matter](https://severalnines.com/blog/my-favorite-postgresql-queries-and-why-they-matter).
- [PostgreSQL Features You May Not Have Tried But Should](https://pgdash.io/blog/postgres-features.html?h).
- [A health check playbook for your Postgres database](https://www.citusdata.com/blog/2019/03/29/health-checks-for-your-postgres-database/).

General:
- [Bye bye Mongo, Hello Postgres ](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres).
- [Modern SQL](https://modern-sql.com/).
- [Use the index, Luke](https://use-the-index-luke.com/).

Command line usage:

- [CartoDB PostgreSQL workshop](https://github.com/CartoDB/labs-postgresql/blob/master/workshop/psql.md).
- [PostgreSQL on the command line](http://phili.pe/posts/postgresql-on-the-command-line).
- [Improving the command line PostgreSQL experience](https://robots.thoughtbot.com/improving-the-command-line-postgres-experience).
- [Generating short ids in Postgres](https://blog.andyet.com/2016/02/23/generating-shortids-in-postgres/?utm_source=postgresweekly&utm_medium=email).

Data types:
- [Postgresql Interval, Date, Timestamp and Time Data Types](https://www.2ndquadrant.com/en/blog/know-what-time-it-is/).

Indexes:
- [Indexes in PostgreSQL — 6 (SP-GiST)](https://habr.com/en/company/postgrespro/blog/446624/) (the full series about indexes is great).
- [An Overview of the Index Changes in PostgreSQL 11](https://severalnines.com/blog/overview-index-changes-postgresql-11).

Partitioning:
- [Partitioning enhancements in PostgreSQL 12 ](https://www.2ndquadrant.com/en/blog/partitioning-enhancements-in-postgresql-12/).

Copying:
- [Fastest Way to Load Data Into PostgreSQL Using Python](https://hakibenita.com/fast-load-data-python-postgresql).
