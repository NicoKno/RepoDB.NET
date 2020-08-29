<h5 class="center code-title">Targeted</h5>

Make a targeted operations within your entity models and/or dynamic objects. [Learn more](/feature/targeted)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entity = new Customer
    {
        Id = 10045,
        Name = "James",
        LastUpdatedUtc = DateTime.UtcNow
    }
    var fields = Field.Parse<Customer>(e => new { e.Id, e.Name });
    var rows = connection.Merge<Customer>(entity, fields: fields);
}
```
