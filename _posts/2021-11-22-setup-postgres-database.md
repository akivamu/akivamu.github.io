---
layout: post
title: Setup new POSTGRES database
tags:
- Security
---

# Setup new POSTGRES database

## Create database and owner
First, create database and the owner of this database.  
Run these command with user has sufficient permissions:

```sql
-- Create db owner user
create user mydb_admin with password '123456'

-- Create db with above user as owner
create database mydb owner mydb_admin
```

The owner can do anything with the database and any objects belongs to that database.

## Grant privileges to other users

You can create other users, but you will need to **grant privileges** to those users.

[See list of privileges](https://www.postgresql.org/docs/13/ddl-priv.html).

[How to grant](https://www.postgresql.org/docs/13/sql-grant.html).

## Grant privileges to future created objects

[Alter default privileges](https://www.postgresql.org/docs/14/sql-alterdefaultprivileges.html).
