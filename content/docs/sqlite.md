---
weight: 700
title: "SQLite"
description: "Single file and fast SQL database"
icon: "article"
date: "2025-01-26T14:16:02+02:00"
lastmod: "2025-01-26T14:16:02+02:00"
draft: false
toc: true
---

### SQLite

The database with all the tables is just a single file. The database is created in the directory where the command is issued. You cannot list databases or tables if you are not in the directory where the database is.

- Install:

```
$ sudo apt install sqlite3
```

- Create database:

```
$ sqlite3 mydatabase.db
```

- Work with an already created database:

```
$ sqlite3 mydatabase.db
or
sqlite> .open mydatabase.db
```

- Create table by importing a CSV file:

```
sqlite> .mode csv
sqlite> .import data.csv mytable
```

- Create table by importing SQL table:

```
sqlite> .read mytable.sql
```

- Query database:

```
sqlite> .mode box
sqlite> select * from mytable;
sqlite> select * from mytable where Abilities='FR(100)';
sqlite> select * from mytable where Abilities like '%FR%';
```

- Delete table:

```
sqlite> drop table `mytable`;
```

- Delete database:

```
$ rm mydatabase.db
```

- List databases and tables:

```
$ sqlite3
sqlite> .databases
sqlite> .open mydatabase
sqlite> .tables
```

The commands must be issued in the directory where the database is.

- Export a table to CSV file:

```
sqlite> .mode csv
sqlite> .headers on
sqlite> .out mytable.csv
sqlite> select * from mytable;
```

- Export a table to SQL file:

```
sqlite> .out mytable.sql
sqlite> .dump mytable
```

- Exit SQLite:

```
sqlite> .exit       # or .quit
```

- Database backup:

```
$ cp database.db database.db.bak                    # if db is only used to read
$ sqlite3 database.db ".backup 'database.db.bak'"   # if db is used to write and there are more users working on the db
```

- Database backup for storage and restoration:

```
$ sqlite3 my_database .dump | gzip -c > my_database.dump.gz # backup
$ zcat my_database.dump.gz | sqlite3 my_database            # restoration
```

### When SQLite is not recommended

If any of the following applies, don't use SQLite:

- Big data.
- Concurrent writers.
- Long running write transactions.
- Remote database: need to run database in a separate server.
- Ephemeral, serveless environment.
