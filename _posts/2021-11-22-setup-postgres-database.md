---
layout: post
title: Setup new POSTGRES database
tags:
- Security
---

# Setup new POSTGRES database

## Create database and owner

Here we will:
- Create new database `mydb`
- Create new user `mydb_admin`
- Set new database owner to `mydb_admin`

Run these command with user has sufficient permissions (could create db):

```sql
-- Create db (owner is current user)
create database mydb

-- Create db owner user
create user mydb_admin with password '<your password>'

-- Change owner
alter database mydb owner to mydb_admin
```

The owner can do anything with the database and any objects belongs to that database.

## Grant privileges to other users

You can create other users, but you will need to **grant privileges** to those users.

[See list of privileges](https://www.postgresql.org/docs/13/ddl-priv.html).

[How to grant](https://www.postgresql.org/docs/13/sql-grant.html).

## Grant privileges to future created objects

[Alter default privileges](https://www.postgresql.org/docs/14/sql-alterdefaultprivileges.html).
