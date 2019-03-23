---
layout: post
title: IEnumerable vs IQueryable
tags:
- DotNet
---

# LINQ

[LINQ to Entities, LINQ to SQL and LINQ to Dataset](https://stackoverflow.com/questions/2443836)  
[LINQ to Entities vs LINQ to Objects](https://stackoverflow.com/questions/7192040)

Summary:

- LINQ-to-Objects is a set of extension methods on `IEnumerable<T>` that allow you to perform **in-memory** query operations on arbitrary sequences of objects.
- LINQ-to-Entities is a LINQ provider that has a set of extension methods on `IQueryable<T>`. The methods build up an expression tree (which is why delegates are actually passed as Expression<>s), and the provider will build up a SQL query based on its parsing of that expression tree.

# IEnumerable vs IQueryable

Looking at the source code, we can see `IQueryable` extends `IEnumerable`:
```
public interface IQueryable : IEnumerable
{
    Type ElementType { get; }
    Expression Expression { get; }
    IQueryProvider Provider { get; }
}
```

Consider a `DbContext`:
```
public class MyDbContext : DbContext
{
    public DbSet<Employee> Employee { get; set; }
}
```

Abstract class `DbSet` implements both `IQueryable` and `IEnumerable`.

Let's do some experiments.

## Use `IEnumerable`

Here we access to `DbSet` as an `IEnumerable`
```
IEnumerable<Employee> enumerable = dbContext.Employee;
enumerable.Count();
```

To show generated SQL query to console, you can call `DbContextOptionsBuilder.UseLoggerFactory()` when create `DbContext`.  
Or use any SQL profiler, we can see the SQL query is like:
```
SELECT "e"."Id", "e"."IsActive", "e"."Name"
FROM "Employee" AS "e"
```
We can see with this query, the data is fetched all rows to memory first, then the `Count()` is applied on in-memory data.

## Use `IQueryable`

```
IQueryable<Employee> queryable = dbContext.Employee;
queryable.Count();
```

Print log or use any SQL profiler, we can see the SQL query is like:
```
SELECT COUNT(*)
FROM "Employee" AS "e"
```
We can see with this query, only the count number is fetched and add to memory, not all data.  
The `Count()` is not applied to in-memory data, but combined into single query to be executed in server.

## Summary

In the case of `IEnumerable`, the code line `IEnumerable<Employee> enumerable = dbContext.Employee` creates an enumerable and forces the later query (`Count()`) to perform as LINQ-to-Objects (in-memory LINQ).

In the case of `IQueryable`, we actually use LINQ-to-Entities via `IQueryable`, and it translates into the final query to be executed in server.

It's clearly that in this example, `IQueryable` has better performance than `IEnumerable`, but [not all methods are supported in this smart ways](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/ef/language-reference/supported-and-unsupported-linq-methods-linq-to-entities).