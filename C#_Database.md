## Table of Contents
1. [Database & Entity Framework](#database--entity-framework)
   - [ADO.NET](#adonet)
   - [Dapper](#dapper)
   - [Raw SQL Execution in EF Core](#raw-sql-execution-in-ef-core)
   - [Third-Party ORMs](#third-party-orms)
   - [Direct Database Client Libraries](#direct-database-client-libraries)
   - [Entity Framework](#entity-framework)
   - [Entity Framework Core](#entity-framework-core)
2. [LINQ (Language Integrated Query)](#linq-language-integrated-query)  

## Database & Entity Framework

### ADO.NET

- **Connected Architecture**: DataReader, Connection, Command  
- **Disconnected Architecture**: DataSet, DataAdapter

**ADO.NET (Active Data Objects for .NET)**

- **What**: Low-level data access using `SqlConnection`, `SqlCommand`, `SqlDataReader`, etc.
- **Use case**: When you need fine-grained control or maximum performance.

**Connection → Command → Execute**

```csharp
using System.Data.SqlClient;

string connectionString = "your-connection-string";
string query = "SELECT Id, Name FROM Users";

using (var connection = new SqlConnection(connectionString))
using (var command = new SqlCommand(query, connection))
{
    connection.Open();
    using (var reader = command.ExecuteReader())
    {
        while (reader.Read())
        {
            int id = reader.GetInt32(0);
            string name = reader.GetString(1);
            // Process data...
        }
    }
}
```

#### ADO.NET Components

- **Connection** (`SqlConnection`, `OracleConnection`, etc.): Manages the connection to the database.
- **Command** (`SqlCommand`, `OracleCommand`, etc.): Executes SQL queries or stored procedures.
- **DataReader** (`SqlDataReader`, etc.): Reads data from the database in a fast, forward-only, read-only manner.
- **DataAdapter**: Bridges data between a database and a DataSet for disconnected operations.
- **DataSet/DataTable**: In-memory representations of data for disconnected scenarios.

#### Connected vs Disconnected Architecture

**Connected Way:**
- The application maintains an open connection to the database while reading or writing data.
- Connection remains open during data operations.
- Fast and efficient for reading large amounts of data sequentially.
- Minimal memory usage.
- Not suitable for editing data in memory and then saving changes later.
- Uses `SqlDataReader` (or similar) to fetch data in a forward-only, read-only manner.

```csharp
using (var connection = new SqlConnection(connectionString))
using (var command = new SqlCommand("SELECT * FROM Users", connection))
{
    connection.Open();
    using (var reader = command.ExecuteReader())
    {
        while (reader.Read())
        {
            // Process each row
        }
    }
}
```

**Disconnected Way:**
- The application fetches data into an in-memory object (like DataSet or DataTable), then closes the connection. Data can be manipulated in memory and changes can be sent back to the database later.
- Uses DataAdapter to fill DataSet or DataTable.
- Connection is open only while fetching or updating data.
- Data can be edited, filtered, or related in memory.
- Suitable for scenarios where you need to work with data offline or batch updates.
- More memory usage than connected way.

```csharp
var dataTable = new DataTable();
using (var connection = new SqlConnection(connectionString))
using (var adapter = new SqlDataAdapter("SELECT * FROM Users", connection))
{
    adapter.Fill(dataTable); // Connection opens, fetches, closes
}
// Work with dataTable in memory
```

**Summary:**
- **Connected**: Open connection, process data row by row, minimal memory.
- **Disconnected**: Load data into memory, close connection, work with data offline.

> **Note:** Entity Framework Core (and other ORMs) use a disconnected approach by default, tracking changes in memory and applying them when you call `SaveChanges()`. For high-performance, read-only, or streaming scenarios, use the connected approach with ADO.NET DataReader.

#### ADO.NET CRUD Operations

**1. Create (Insert)**

```csharp
using (var connection = new SqlConnection(connectionString))
using (var command = new SqlCommand("INSERT INTO Users (Name, Email) VALUES (@Name, @Email)", connection))
{
    command.Parameters.AddWithValue("@Name", "Alice");
    command.Parameters.AddWithValue("@Email", "alice@example.com");
    connection.Open();
    command.ExecuteNonQuery();
}
```

**2. Read (Select)**

```csharp
using (var connection = new SqlConnection(connectionString))
using (var command = new SqlCommand("SELECT Id, Name, Email FROM Users WHERE Id = @Id", connection))
{
    command.Parameters.AddWithValue("@Id", 1);
    connection.Open();
    using (var reader = command.ExecuteReader())
    {
        if (reader.Read())
        {
            int id = reader.GetInt32(0);
            string name = reader.GetString(1);
            string email = reader.GetString(2);
            // Use the data
        }
    }
}
```

**3. Update**

```csharp
using (var connection = new SqlConnection(connectionString))
using (var command = new SqlCommand("UPDATE Users SET Name = @Name WHERE Id = @Id", connection))
{
    command.Parameters.AddWithValue("@Name", "Alice Smith");
    command.Parameters.AddWithValue("@Id", 1);
    connection.Open();
    command.ExecuteNonQuery();
}
```

**4. Delete**

```csharp
using (var connection = new SqlConnection(connectionString))
using (var command = new SqlCommand("DELETE FROM Users WHERE Id = @Id", connection))
{
    command.Parameters.AddWithValue("@Id", 1);
    connection.Open();
    command.ExecuteNonQuery();
}
```

#### Execute Methods

| Method | Description |
|--------|-------------|
| `ExecuteNonQuery` | Executes a SQL statement that does not return any rows (e.g., INSERT, UPDATE, DELETE, DDL), returns number of rows affected |
| `ExecuteReader` | Executes a SQL statement that returns rows (e.g., SELECT). Returns a DataReader for reading the result set row by row |
| `ExecuteScalar` | Executes a SQL statement and returns a single value (the first column of the first row) |
| `ExecuteXmlReader` | (SQL Server only) Executes a command and returns the results as an XmlReader |

---

### Dapper

**What:** A lightweight micro-ORM that extends ADO.NET for easy mapping between queries and objects.

**Use cases:** When you want performance and simple mapping without full EF Core overhead. It is designed to provide fast and simple data access, sitting on top of ADO.NET and mapping query results to C# objects with minimal overhead.

```bash
dotnet add package Dapper
```

```csharp
using Dapper;
using System.Data.SqlClient;

string connectionString = "your-connection-string";
using (var connection = new SqlConnection(connectionString))
{
    // Query
    var users = connection.Query<User>("SELECT Id, Name FROM Users").ToList();
    
    // Query with parameters
    var user = connection.QueryFirstOrDefault<User>(
        "SELECT Id, Name FROM Users WHERE Id = @Id", new { Id = 1 });
    
    // Insert
    connection.Execute("INSERT INTO Users (Name) VALUES (@Name)", new { Name = "Alice" });
}
```

**When to use Dapper:**
- When you want raw SQL performance with object mapping.
- When you don't need change tracking or complex relationship management.
- For microservices, APIs, or apps where you want to control SQL and performance.

> **Note:** `QueryFirstOrDefaultAsync` is a Dapper extension method used to execute a SQL query and map the first row of the result set to a .NET object asynchronously. If no rows are returned, it returns the default value for the type (usually null for reference types).

---

### Raw SQL Execution in EF Core

**What:** Execute raw SQL queries or stored procedures directly via EF Core.

**Use case:** When you need to run complex queries or leverage existing stored procedures.

```csharp
var users = dbContext.Users.FromSqlRaw("SELECT * FROM Users").ToList();
```

---

### Third-Party ORMs

**Examples:** NHibernate, ServiceStack.OrmLite, LLBLGen Pro, etc.

**Use case:** When you have specific requirements or legacy code using these libraries.

- Only use if your legacy system already uses it.
- Not advisable for new projects, as these libraries may not have maximum community support, and EF Core performs most tasks efficiently.

---

### Direct Database Client Libraries

**What:** Use database-specific libraries (e.g., Npgsql for PostgreSQL, MySqlConnector for MySQL).

**Use case:** When you need features or performance not exposed by ADO.NET or ORMs.

- Not advisable to use, as mostly EF Core or Dapper is sufficient.
- If used, may not have support for LINQ.
- Only use in very special cases where functionality you need is only available in database-specific libraries.

---

### Entity Framework

- **Code First**: Database from model  
- **Database First**: Model from database  
- **Model First**: Visual model design

### Entity Framework Core

#### Database First vs Code First

- **Database First**: Generate model from existing database  
- **Code First**: Generate database from model classes

**Entity Framework Core (EF Core)**

- **What**: An ORM (Object Relational Mapper) that lets you work with databases using .NET objects.
- **Use case**: Most common for CRUD operations, LINQ queries, and code-first or database-first approaches.

**Define DbContext:**

```csharp
public class MyDbContext : DbContext
{
    public DbSet<User> Users { get; set; }

    public MyDbContext(DbContextOptions<MyDbContext> options) : base(options) { }
}
```

**Register in Program.cs:**

```csharp
builder.Services.AddDbContext<MyDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

**Features:**

- **ORM** (Object-Relational Mapping): Maps .NET classes to database tables and .NET properties to table columns.
- **LINQ Support**: Query the database using LINQ (Language Integrated Query) for type-safe, compile-time-checked queries.
- **Migrations**: Manage database schema changes through code, enabling version control of the database structure.
- **Cross-Platform**: Works on Windows, Linux, and macOS.
- **Multiple Database Providers**: Supports SQL Server, PostgreSQL, MySQL, SQLite, Oracle, and more via providers.
- **Change Tracking**: Automatically track changes to objects so you can save updates efficiently.
- **Lazy/Eager/Explicit Loading**: Flexible loading of related data.

**Define Entity and DbContext:**

```csharp
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class AppDbContext : DbContext
{
    public DbSet<User> Users { get; set; }

    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
}
```

**Register AppDbContext:**

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

// AddDbContext registers your context as scoped by default, so you get a new instance per HTTP request, not a singleton for the whole application. This is the recommended and safe approach for EF Core.
```

#### Adding New Columns

// 1\. Add property to model

public class User

{

    public int Id { get; set; }

    public string Name { get; set; }

    public string NewColumn { get; set; } // New property

}

// 2\. Add migration

dotnet ef migrations add AddNewColumnToUser

// 3\. Apply migration

dotnet ef database update

#### CRUD Operations

**1. Add (Create)**

```csharp
var user = new User { Name = "Alice", Email = "alice@example.com" };
context.Users.Add(user);
await context.SaveChangesAsync();
```

**2. Read**

```csharp
var users = await context.Users.ToListAsync();

var user = await context.Users.FindAsync(1); // by id

var user = await context.Users.FirstOrDefaultAsync(u => u.Email == "alice@example.com"); // with filter
```

**3. Update**

```csharp
var user = await context.Users.FindAsync(1);
if (user != null)
{
    user.Name = "Alice Smith";
    await context.SaveChangesAsync();
}
```

**4. Delete**

```csharp
var user = await context.Users.FindAsync(1);
if (user != null)
{
    context.Users.Remove(user);
    await context.SaveChangesAsync();
}
```

#### Bulk Operations

Using `EFCore.BulkExtensions`:

```csharp
var users = new List<User>
{
    new User { Name = "Alice" },
    new User { Name = "Bob" }
};

// Bulk Insert
await context.BulkInsertAsync(users);

// Bulk Update
await context.BulkUpdateAsync(users);

// Bulk Delete
await context.BulkDeleteAsync(users);
```

**Batching for bulk operations:**

```csharp
context.Users.AddRange(listOfUsers);
await context.SaveChangesAsync();
```

#### Skip & Take (Pagination)

```csharp
int pageNumber = 2;
int pageSize = 10;

var users = await context.Users
    .OrderBy(u => u.Id) // Always order before Skip/Take
    .Skip((pageNumber - 1) * pageSize)
    .Take(pageSize)
    .ToListAsync();
```

#### Use in Code

```csharp
public class UserService
{
    private readonly AppDbContext _context;

    public UserService(AppDbContext context)
    {
        _context = context;
    }

    public async Task<List<User>> GetAllUsersAsync() =>
        await _context.Users.ToListAsync();
}
```

#### Code First Approach

- Good for new applications, if developer does not know much about DB concepts.
- You define your C# entity classes and DbContext manually.
- EF Core can generate the database schema and migration files for you using commands like:
  - `dotnet ef migrations add InitialCreate`
  - `dotnet ef database update`
- You write the entity and context classes, but migrations and SQL scripts are auto-generated.
- Migrations are generated automatically by EF Core tools to keep your database schema in sync with your models.

#### Database First

- For existing applications.
- If you already have a database, EF Core can generate C# classes and DbContext for you using the Scaffold-DbContext command.
```powershell
dotnet ef dbcontext scaffold "YourConnectionString" Microsoft.EntityFrameworkCore.SqlServer -o Models
```
- This creates entity classes and a DbContext based on your existing database schema.

#### Migrations

- When you enter migration command → it will create migrations files → which contains basically up(create) or down(drop) commands → when you send update command → changes will be applied to DB.

#### Good Approaches / Best Practices

- Use `AsNoTracking()` for read-only queries: `var users = await context.Users.AsNoTracking().ToListAsync();`
- Use async methods (`ToListAsync`, `FirstOrDefaultAsync`, etc.) to avoid blocking threads and improve scalability.
- Use `.Include()` and `.ThenInclude()` to eagerly load related data when needed.
- Consider projection (e.g., with `.Select()`) to fetch only required data.
- Use pagination (`Skip`/`Take`) for large result sets.
- Avoid loading entire tables or large object graphs.

#### Concurrency

- In EF Core concurrency handled by `RowVersion` (concurrency token).

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }

    [Timestamp]
    public byte[] RowVersion { get; set; }
}
```

- In your DbContext, EF Core will automatically check the `RowVersion` during `SaveChangesAsync()`.
- If another update happened since you loaded the entity, a `DbUpdateConcurrencyException` is thrown.

```csharp
try
{
    await context.SaveChangesAsync();
}
catch (DbUpdateConcurrencyException)
{
    // Handle concurrency conflict (e.g., reload, inform user)
}
```

### Stored Procedures vs Functions

#### Stored Procedures

- Can return multiple result sets  
- Can perform DML operations (INSERT, UPDATE, DELETE)  
- Better for complex business logic

#### Functions

- Must return single result set  
- Cannot perform DML operations  
- Better for calculations and data transformation

| Feature | Stored Procedure | Function |
| :---- | :---- | :---- |
| Return | Multiple result sets | Single value/table |
| Transaction | Can use transactions | Cannot use transactions |
| DML Operations | Can perform INSERT/UPDATE/DELETE | Cannot modify data |

### Optimization Techniques

- **SQL Profiler**: Analyze query performance  
- **Indexes**: Improve query speed  
- **Triggers**: Automated actions  
- **SET NOCOUNT**: Reduce network traffic (but has disadvantages)

### Table Variables vs Temp Tables

- **Table Variables**: Scope limited to batch/procedure, less logging  
- **Temp Tables**: Can have indexes, better for large datasets

### Data Structures

#### Array vs ArrayList

| Feature | Array | ArrayList |
| :---- | :---- | :---- |
| Type | Strongly typed | Object-based |
| Performance | Faster | Slower (boxing) |
| Size | Fixed | Dynamic |

---


## LINQ (Language Integrated Query)

### Query Syntax vs Method Syntax

// Query Syntax

var query \= from student in students

            where student.Age \> 18

            orderby student.Name

            select student;

// Method Syntax

var result \= students

    .Where(s \=\> s.Age \> 18\)

    .OrderBy(s \=\> s.Name)

    .Select(s \=\> s);

### LINQ Providers

- **LINQ to Objects**: In-memory collections  
- **LINQ to SQL**: Database queries  
- **LINQ to XML**: XML document manipulation  
- **LINQ to Entities**: Entity Framework

### Common LINQ Methods

// Filtering

var result \= collection.Where(x \=\> x.Property \> value);

// Projection

var result \= collection.Select(x \=\> new { x.Property1, x.Property2 });

// Aggregation

var count \= collection.Count();

var sum \= collection.Sum(x \=\> x.Value);

// Ordering

var ordered \= collection.OrderBy(x \=\> x.Property);

### Common LINQ Operations

- **Where**: Filtering  
- **Select**: Projection  
- **OrderBy**: Sorting  
- **GroupBy**: Grouping  
- **Join**: Joining collections  
- **First/FirstOrDefault**: Getting first element  
- **Single/SingleOrDefault**: Getting single element

### LINQ Performance Considerations

- **Deferred execution**: Queries execute when enumerated  
- **Immediate execution**: Use ToList(), ToArray() to execute immediately  
- **Performance optimization**: Use appropriate methods, avoid N+1 queries

---
