---
layout: navpage
sidebar: getstarted
title: "Get Started for SQLite"
description: "Learn on how to work with SQLite databases using RepoDB library."
permalink: /tutorial/get-started-sqlite
tags: [repodb, tutorial, get-started, orm, hybrid-orm, sqlite]
---

# Get Started for SQLite

RepoDB is a hybrid .NET ORM library for [SQLite](https://www.nuget.org/packages/RepoDb.SqLite) RDBMS. The project is hosted at [Github](https://github.com/mikependon/RepoDb/tree/master/RepoDb.SqLite) and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

#### Installation

The library can be installed via Nuget. In your Package Manager Console, type the command below.

```csharp
> Install-Package RepoDb.SqLite
```

Once installed, call the bootstrapper to initialize all the dependencies for SQLite.

```csharp
RepoDb.SqLiteBootstrap.Initialize();
```

Or visit our [installation](/tutorial/installation) page for more information.

#### Create a Table

```csharp
CREATE TABLE IF NOT EXISTS [Person]
(
    Id INTEGER PRIMARY KEY AUTOINCREMENT,
    Name TEXT,
    Age INTEGER,
    CreatedDateUtc DATETIME
);
```

#### Create a Model

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

> The class `model` and the database `table` specified above will be used by the samples further on this tutorial.

#### Creating a Record

To create a record, use the [Insert](/operation/insert) method.

```csharp
var person = new Person
{
    Name = "John Doe",
    Age = 54,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SQLiteConnection(ConnectionString))
{
    var id = connection.Insert(person);
}
```

To insert multiple rows, use the [InsertAll](/operation/insertall) operation instead.

Let us say you had created a method `GetPeople()` that returns a list of `Person`.

```csharp
private IEnumerable<Person> GetPeople(int count = 10)
{
    for (var i = 0; i < count; i++)
    {
        yield return new Person
        {
            Name = $"Person{i}",
            Age = 54,
            CreatedDateUtc = DateTime.UtcNow
        };
    }
}
```

Then simply create a list of `Person` and passed it when you call the [InsertAll](/operation/insertall) method.

```csharp
var people = GetPeople(100);
using (var connection = new SQLiteConnection(ConnectionString))
{
    var rowsInserted = connection.InsertAll(people);
}
```

> The [Insert](/operation/insert) method returns the `Primary` (or `Identity`) field value while the [InsertAll](/operation/insertall) method returns the number of rows inserted. Both methods are automatically setting back the value of the `PrimaryKey` and/or `Identity` property if present..

#### Querying a Record

To query a record, use the [Query](/operation/query) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var person = connection.Query<Person>(e => e.Id == 1);
    /* Do the stuffs for the 'person' here */
}
```

To query all the rows, use the [QueryAll](/operation/queryall) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var people = connection.QueryAll<Person>();
    /* Do the stuffs for the 'people' here */
}
```

#### Merging a Record

To merge a record, use the [Merge](/operation/merge) method.

```csharp
var person = new Person
{
    Id = 1,
    Name = "John Doe",
    Age = 57,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SQLiteConnection(ConnectionString))
{
    var id = connection.Merge(person);
}
```

By default, the `Primary` (or `Identity`) field is used as a qualifier. You can also use specify the customized qualifiers.

```csharp
var person = new Person
{
    Name = "John Doe",
    Age = 57,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SQLiteConnection(ConnectionString))
{
    var id = connection.Merge(person, qualifiers: (p => new { p.Name }));
}
```

To merge all the rows, use the [MergeAll](/operation/mergeall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new SQLiteConnection(ConnectionString))
{
    var affectedRecords = connection.MergeAll<Person>(people);
}
```

> The [Merge](/operation/merge) method returns the `Primary` (or `Identity`) field value while the [MergeAll](/operation/mergeall) method returns the number of rows affected. Both methods are automatically setting back the value of the `PrimaryKey` and/or `Identity` property if present..

#### Deleting a Record

To delete a record, use the [Delete](/operation/delete) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(1);
}
```

By default, it uses the primary key as a qualifier. You can also use the other field.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all the rows, use the [DeleteAll](/operation/deleteall) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

You can also pass the list of primary keys to be deleted.

```csharp
using (var connection = new SQLiteConnection(connectionString))
{
    var primaryKeys = new [] { 10045, 11001, ..., 12011 };
    var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

> Both the [Delete](/operation/delete) and [DeleteAll](/operation/deleteall) methods return the number of rows affected during the execution.

#### Updating a Record

To update a record, use the [Update](/operation/update) method.

```csharp
var person = new Person
{
    Id = 1,
    Name = "James Doe",
    Age = 55,
    DateInsertedUtc = DateTime.UtcNow
};
using (var connection = new SQLiteConnection(ConnectionString))
{
    var updatedRows = connection.Update<Person>(person);
}
```

You can also update via dynamic by targetting certain columns.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var updatedRows = connection.Update("Person", new { Id = 1, Name = "James Doe" });
}
```

To update all the rows, use the [UpdateAll](/operation/updateall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SQLiteConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people);
}
```

By default, the `Primary` (or `Identity`) field is used as a qualifier. You can also specify your custom qualifiers.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SQLiteConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people, qualifiers: (p => new { p.Name }));
}
```

> Both the [Update](/operation/update) and [UpdateAll](/operation/updateall) methods return the number of rows affected during the execution.

#### Executing a Query

To execute a query use the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var affectedRecords = connection.ExecuteNonQuery("DELETE FROM [Person] WHERE Id = @Id;", new { Id = 1 });
}
```

To execute a query while expecting a result of class object, use the [ExecuteQuery](/operation/executequery) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var people = connection.ExecuteQuery<Person>("SELECT * FROM [Person] ORDER BY Id ASC;");
    /* Do the stuffs for the people here */
}
```

To execute a query while expecting a single result, use the [ExecuteScalar](/operation/executescalar) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var maxId = connection.ExecuteQuery<Person>("SELECT MAX(Id) FROM [Person];");
}
```

To execute a query while expecting a result of `DbDataReader`, use the [ExecuteReader](/operation/executereader) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [Person] ORDER BY Id ASC;"))
    {
        /* Do the stuffs for the data reader here */
    }
}
```

#### Typed Result Execution

You can infer the scalar resultset in any .NET CLR type via [ExecuteQuery](/operation/executequery) operation.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var param = new
    {
        Id = 10045
    };
    var name = connection.ExecuteQuery<string>("SELECT Name FROM [Person] WHERE Id = @Id;", param);
}
```

And even with the Enumerations.

```csharp
public enum Gender
{
    Male,
    Female
}
```

Then call it like below.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var param = new
    {
        Id = 10045
    };
    var name = connection.ExecuteQuery<Gender>("SELECT Gender FROM [Person] WHERE Id = @Id;", param);
}
```