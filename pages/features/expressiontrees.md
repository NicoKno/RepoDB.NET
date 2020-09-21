---
layout: navpage
sidebar: features
title: "Expression Trees"
description: "This is a feature that would allow you to compose a conditional expressions (to filter a data) when doing an operation in the database."
permalink: /feature/expressiontrees
tags: [repodb, class, expressiontrees, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Expression Trees

This is a feature that would allow you to compose a conditional expressions (to filter a data) when doing an operation in the database. This condition can be applied in both push/pull operations (i.e.: [Insert](/operation/insert), [Delete](/operation/delete), [Update](/operation/update) and [Query](/opereration/query)).

###### There are 3 ways of composing an expression trees.

- `Anonymous` - it is the most simple and direct way of filterting the results. You can use the anonymous object to filter data.
- `Linq-Expression` - it is the most common way of filtering the data.
- `Query Objects` - it is the most advance, efficient, performant and powerful way of composing the tree expression.

#### Disclaimer

The support to query objects are massive and well tested with high-quality. However, the Linq-Expression parser of the library is not as extensive as Entity Framework. Therefore, we highly recommend to always use the [QueryGroup](/class/querygroup) and [QueryField](/class/queryfield) objects when composing a complex expressions.

#### Anonymous

Below is a sample way of querying via anonymous. 

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(new { Id = 10045 }).FirstOrDefault();
}
```

Or with multiple column.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(new { FirstName = "John", LastName = "Doe" }).FirstOrDefault();
}
```

> Please be aware that the compiler does not understand anonymous, any change on the column name would not trigger a pre-compilation exception. Also, the anonymous types only supported the `expression-equality` and cannot be used with other operations (i.e.: `NotEqual`, `GreaterThan`, etc).

#### Linq-Expression

Below is a sample way of querying via `Linq Expression`. 

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(e => e.Id == 10045).FirstOrDefault();
}
```

Or with multiple column.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(e => e.FirstName == "John" && e.LastName == "Doe" }).FirstOrDefault();
}
```

Or with other operations.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var states = new [] { "Chicago", "Washington", "Kansas" };
    var customers = connection.Query<Customer>(e => states.Contains(e.State) && e.IsActive == false && e.DateOfBirth >= DateTime.Parse("1970-01-01") });
}
```

#### Query Objects

Below is a sample way of querying via [QueryField](/class/queryfield). 

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(new QueryField(nameof(Customer.Id), 10045)).FirstOrDefault();
}
```

Or with multiple column.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new []
    {
        new QueryField(nameof(Customer.FirstName), "John"),
        new QueryField(nameof(Customer.LastName), "Doe")
    };
    var customer = connection.Query<Customer>(where).FirstOrDefault();
}
```

Or with other operations.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var states = new [] { "Chicago", "Washington", "Kansas" };
    var where = new []
    {
        new QueryField(nameof(Customer.State), Operation.In, states),
        new QueryField(nameof(Customer.IsActive),false),
        new QueryField(nameof(Customer.DateOfBirth), Operation.GreaterThanOrEqual, DateTime.Parse("1970-01-01"))
    };
    var customers = connection.Query<Customer>(where);
}
```
