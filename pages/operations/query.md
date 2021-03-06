---
layout: default
sidebar: operations
title: "Query"
permalink: /operation/query
tags: [repodb, tutorial, query, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: OPERATIONS
---

# Query

---

This method is used to query a row from the table.

### Code Snippets

Below is the sample code to fetch a row from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(10045).FirstOrDefault();
}
```

You can also query via expression.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(e => e.Id == 10045).FirstOrDefault();
}
```

Or like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(
        e => e.FirstName == "John" && e.LastName == "Doe").FirstOrDefault();
}
```

> It always returns an `IEnumerable<T>` object even if your result is one.

### Targeting a Table

You can also target a specific table by passing the literal table like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>("[dbo].[Person]",
        10045).FirstOrDefault();
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query("[dbo].[Person]",
        10045).FirstOrDefault();
}
```

> The result is a list of dynamic objects of type `ExpandoObject`.

### Specific Columns

You can also target a specific columns to be queried by passing the list of fields to be included in the `fields` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var fields = Field.Parse<Person>(e => new
    {
        e.Id,
        e.Name,
        e.DateOfBirth,
        e.DateInsertedUtc
    });
    var person = connection.Query<Person>(e => e.Id == 10045,
        fields: fields).FirstOrDefault();
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query("[dbo].[Person]",
        new { Id = 10045 },
        fields: Field.From("Id", "Name", "DateOfBirth", "DateInsertedUtc")).FirstOrDefault();
}
```

### Type Result

You can also directly infer the resultset into a `string` type.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var names = connection.Query<string>(ClassMappedNameCache.Get<Person>(),
        new QueryField("Name", Operation.Like, "%Anders%"),
        fields: Field.From(nameof(Person.Name))).FirstOrDefault();
}
```

**Note:** Inferrence works in all types but not from this operation. The other non-class type (i.e.: `long`, `int`, `System.DateTime`, etc) cannot be inferred as the `TEntity` generic type is filtered as `class`. Please see the [ExecuteQuery](/operation/executequery) operation for the support to the other types.

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(10045,
        hints: "WITH (NOLOCK)").FirstOrDefault();
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(10045,
        hints: SqlServerTableHints.TabLock).FirstOrDefault();
}
```

### Ordering the Results

To order the results, you have to pass an array of `OrderField` objects in the `orderBy` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new
    {
        LastName = Order.Descending,
        FirstName = Order.Ascending
    });
    var people = connection.Query<Person>(e => e.IsActive == true,
        orderBy: orderBy);
    // Do the stuffs for 'people' here
}
```

### Filtering the Results

To filter the results, you have to pass a value at the `top` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == true,
        top: 100);
    // Do the stuffs for 'people' here
}
```

### Caching the Results

To cache the results, simply pass a literal string key into the `cacheKey` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == true,
        cacheKey: "CackeKey:ActivePeople");
    // Do the stuffs for 'people' here
}
```

> The cache expiration is defaulted to 180 minutes. You can override it by passing an integer value at the `cacheExpirationInMinutes` argument.
