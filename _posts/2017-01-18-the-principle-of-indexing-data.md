---
layout: post
title: Simple explanation about indexing data
tags:
- DevOps
---

[Reference from SoF](https://stackoverflow.com/a/1130/6445037)

# Our original database

Suppose we have this table which stores all our data:
```
Table: users

Field name        Data type       Size on disk
----------------------------------------------
id (Primary key)  Unsigned INT    4 bytes
firstName         Char(50)        50 bytes
lastName          Char(50)        50 bytes
emailAddress      Char(100)       100 bytes

With 1,000,000 records
```

# Search by id

Here, the column `id` is integer, unique primary key, auto increase when insert.

Using simplest searching algorithm: we loop through all rows to find match record. It takes 1,000,000 operations.

Now assume the database is sorted by `id`, we can use binary search algorithm - O(logN), which reduce to 20 operations.

# Search by firstName

Since column `firstName` is string, we have to loop through all records, compare the value...  
It takes 1,000,000 operations.

## Do indexing the database by firstName

Now we create new table with 2 columns:
```
Table: indexByFirstName

Field name        Data type       Size on disk
----------------------------------------------
firstName         Char(50)        50 bytes
recordPointer     Special         4 bytes

With 1,000,000 records
```

Column `firstName` is as before.  
Column `recordPointer` is special type, which points to full records in table `users`.

Example row: ('bob', 0x1234)

Now, we sort this table, make it able to be searched by binary search algorithm.  


This table is called an index.  
The process to create this table is called indexing.

## Search by firstName with indexing

Now we can use binary search algorithm, to find a row in the index. The cost is reduced to O(logN): 20 operations.

After row found, we use `recordPointer` to get full data from table `users`. Cost 1 more operation, total 21 operations.
