# C# Async, Garbage Collection, Logging

## Table of Contents

1. [Asynchronous Operations](#1-asynchronous-operations)
   - [async and await Keywords](#async-and-await-keywords)
   - [Task and Task<T>](#task-and-taskt)
   - [Example: Async Method](#example-async-method)
   - [Usage Notes](#usage-notes)
2. [Cancellation Token](#2-cancellation-token)
   - [CancellationTokenSource](#cancellationtokensource)
   - [Properties](#properties)
   - [Methods](#methods)
   - [Example: Using CancellationToken with Database Operations](#example-using-cancellationtoken-with-database-operations)
3. [Garbage Collection](#3-garbage-collection)
   - [Generational GC Model](#generational-gc-model)
   - [GC Process Flow](#gc-process-flow)
   - [Explicit GC Collection](#explicit-gc-collection)
4. [Finalizer](#4-finalizer)
   - [How It Works](#how-it-works)
   - [Example: Finalizer Implementation](#example-finalizer-implementation)
   - [Limitations](#limitations)
5. [IDisposable](#5-idisposable)
   - [Best Practices: The Dispose Pattern](#best-practices-the-dispose-pattern)
6. [Logging](#6-logging)
   - [Microsoft.Extensions.Logging](#microsoftextensionslogging)
   - [Structured Logging with Serilog](#structured-logging-with-serilog)
   - [Azure Application Insights](#azure-application-insights)

---

## 1. Asynchronous Operations

### async and await Keywords

- **async keyword**: Marks a method as asynchronous.
- **await keyword**: Pauses execution until the awaited task completes, without blocking the thread.
- **Task and Task<T>**: Represent ongoing operations that will complete in the future.

### Task and Task<T>

The method which is marked with the `async` keyword must return a `Task` or `Task<T>`. The idea of a `Task` is that it represents an asynchronous operation and does not return anything. In the case of `Task<T>`, it is like a promise that in the future this method will return a value of the data type `T`.

**UI --> API -> Database:**
- You cannot explore it
- Hence do not wait to get it complete, and block the main thread
- Use `async` and `await` and move forward with different task

**UI --> API:**
- Very big independent tasks like calculations
- Use parallelism to run it simultaneously

### Example: Async Method

```csharp
public async Task<string> GetDataAsync()
{
    // Simulate an asynchronous operation (e.g., HTTP call)
    await Task.Delay(1000);
    return "Data loaded";
}
```

**Usage:**

```csharp
string result = await GetDataAsync();
```

- The method returns a `Task<string>`.
- `await` releases the thread to do other work while the task completes.

**Execution Flow:**

```csharp
public async Task MyMethodAsync()
{
    Console.WriteLine("Step 1: Before await");
    
    await Task.Delay(2000); // Simulate async work (e.g., database call)
    
    Console.WriteLine("Step 2: After await");
}
```

Output: `print before await` --> `2 sec loading` --> `print after await`

### Usage Notes

- Use `async/await` for I/O-bound operations (database, file, network).
- Avoid `async void` except for event handlers; use `async Task` or `async Task<T>`.
- Do not block on `async` code (avoid `.Result` or `.Wait()`).
- The idea is to avoid blocking a thread while waiting for a response, either from an external system such as a web service or from the computer's file management system.

---

## 2. Cancellation Token

You can use a `CancellationToken` to signal to a running task that it should stop executing. You can cancel a long-running Task in C# by using a `CancellationToken` and periodically checking whether cancellation has been requested within the task's logic.

### CancellationTokenSource

- It is a class implementing the `IDisposable` interface. `CancellationTokenSource` signals to a `CancellationToken` that it should be canceled.

**Constructors:**

| Constructor | Description |
|-------------|-------------|
| `CancellationTokenSource()` | Initializes a new instance of the `CancellationTokenSource` class. |
| `CancellationTokenSource(TimeSpan delay)` | Initializes a new instance of the `CancellationTokenSource` class that will be canceled after the specified time span. |
| `CancellationTokenSource(int millisecondsDelay)` | Initializes a new instance of the `CancellationTokenSource` class that will be canceled after the specified delay in milliseconds. |

### Properties

| Property | Description |
|----------|-------------|
| `public bool IsCancellationRequested { get; }` | Gets whether cancellation has been requested for this `CancellationTokenSource`. Returns `true` if the cancellation has been requested; otherwise, `false`. |
| `public CancellationToken Token { get; }` | Gets the `CancellationToken` associated with this `CancellationTokenSource`. It will throw `ObjectDisposedException` if the token source has been disposed. |

### Methods

| Method | Description |
|--------|-------------|
| `Cancel()` | Communicates a request for cancellation. |
| `Cancel(bool throwOnFirstException)` | Communicates a request for cancellation and specifies whether remaining callbacks and cancellable operations should be processed if an exception occurs. The parameter `throwOnFirstException` specifies `true` if exceptions should immediately propagate; otherwise, `false`. |
| `CancelAfter(TimeSpan delay)` | Schedules a cancel operation on this `CancellationTokenSource` after the specified time span. The parameter `delay` specifies the time span to wait before canceling this `CancellationTokenSource`. |
| `CancelAfter(int millisecondsDelay)` | Schedules a cancel operation on this `CancellationTokenSource` after the specified number of milliseconds. The parameter `millisecondsDelay` specifies the time span to wait before canceling this `CancellationTokenSource`. |
| `Dispose()` | Releases all resources used by the current instance of the `CancellationTokenSource` class. |

### Example: Using CancellationToken with Database Operations

```csharp
async Task RunStoredProcedureAsync(CancellationToken cancellationToken)
{
    using (var connection = new SqlConnection(connectionString))
    using (var command = new SqlCommand("myStoredProcedure", connection))
    {
        command.CommandType = CommandType.StoredProcedure;
        // Add parameters as needed

        await connection.OpenAsync(cancellationToken);
        
        // Pass the token to ExecuteReaderAsync, ExecuteNonQueryAsync, etc.
        using (var reader = await command.ExecuteReaderAsync(cancellationToken))
        {
            while (await reader.ReadAsync(cancellationToken))
            {
                // Process results
            }
        }
    }
}
```

---

## 3. Garbage Collection

.NET Core uses a generational GC model, which divides objects into three generations:

### Generational GC Model

| Generation | Description |
|------------|-------------|
| **Generation 0 (Gen 0)** | Newly allocated objects. Collection is fast and frequent. |
| **Generation 1 (Gen 1)** | Objects that survived Gen 0 collection. |
| **Generation 2 (Gen 2)** | Long-lived objects that survived multiple collections. |

### GC Process Flow

```
[App Allocates Objects] --> [Managed Heap]
            |
            v
      [GC Roots] traverse --> [Live Objects]
            |
            v
      [Mark & Compact]
            |
            v
[Unreachable Objects Freed]
```

### Explicit GC Collection

- Explicit calls to `GC.Collect()` (not recommended in most cases).

---

## 4. Finalizer

If a class defines a finalizer (`~MyClass()`), GC will call it before reclaiming the object's memory.

- Finalizer can be defined with `~classname()`
- Used to clear unmanaged resources
- GC is not aware about unmanaged resource (like file handles, database connections, native memory), hence need to define finalizer, which will be clearing unmanaged resources
- GC will call finalizer method before reclaiming the object

### How It Works

- The GC automatically calls the finalizer (if defined) when it determines that the object is no longer accessible.
- The timing is non-deterministic; you cannot predict exactly when the finalizer will run.
- The finalizer runs on a separate thread, not the thread that created the object.

### Example: Finalizer Implementation

```csharp
class MyClass
{
    // Finalizer
    ~MyClass()
    {
        // cleanup code for unmanaged resources
    }
}
```

### Limitations

- You cannot call a finalizer directly; it is called by the GC.
- Finalizers can delay memory reclamation because the object survives at least one GC cycle after becoming unreachable.
- If you forget to release unmanaged resources, you may cause resource leaks.

---

## 5. IDisposable

For deterministic resource cleanup, implement `IDisposable` and call `Dispose()` manually.

- The `IDisposable` interface provides a standard way to release unmanaged resources deterministically (i.e., at a known time).
- It defines a single method: `void Dispose()`;
- You implement `IDisposable` when your class holds unmanaged resources or references to other `IDisposable` objects.

### How It Works

- The consumer of your class calls `Dispose()` when the object is no longer needed.
- This allows you to free resources immediately, rather than waiting for the GC.

```csharp
class MyClass : IDisposable
{
    // Implementation
}
```

### Best Practices: The Dispose Pattern

```csharp
class MyClass : IDisposable
{
    private bool disposed = false;

    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this); // Prevents finalizer from running
    }

    protected virtual void Dispose(bool disposing)
    {
        if (!disposed)
        {
            if (disposing)
            {
                // Free managed resources here
            }
            // Free unmanaged resources here

            disposed = true;
        }
    }

    ~MyClass()
    {
        Dispose(false);
    }
}
```

- If your class owns unmanaged resources directly, implement both a finalizer and `IDisposable`.
- If your class only uses managed resources that themselves implement `IDisposable`, just implement `IDisposable` (no finalizer needed).
- Always call `GC.SuppressFinalize(this)` in `Dispose()` to avoid unnecessary finalization.

---

## 6. Logging

- Logging in built-in .NET can be configured inside the `Microsoft.Extensions.Logging` namespace. You can log messages at different levels (Information, Warning, Error, etc.) and output them to various providers (Console, Debug, Files, etc.).

### Microsoft.Extensions.Logging

```csharp
using Microsoft.Extensions.Logging;

public class MyService
{
    private readonly ILogger<MyService> _logger;

    public MyService(ILogger<MyService> logger)
    {
        _logger = logger;
    }

    public void DoWork()
    {
        _logger.LogInformation("Doing work at {Time}", DateTime.UtcNow);
        _logger.LogWarning("Warning message");
        _logger.LogError("An error occurred");
    }
}
```

`ILogger<MyService> _logger` - here we pass `MyService`, so that service name can be logged to identify from where logs come, and used for log filtering.

### Structured Logging with Serilog

- Logs are sent to Elasticsearch for search and analysis, often via Serilog or ELK stack.
- Serilog is a popular structured logging library for .NET applications. It is designed to make log data more useful, flexible, and easy to analyze, especially in modern distributed and cloud-based systems.
- Traditional logging writes plain text messages. Structured logging captures not just the message, but also key-value pairs (properties) that describe the context of the event. This makes logs easier to search, filter, and analyze—especially when sent to systems like Elasticsearch, Seq, or Splunk.
- A "sink" is a Serilog term for a log destination.
- The `Serilog.Sinks.Elasticsearch` package allows you to send logs directly to Elasticsearch.

```powershell
dotnet add package Serilog.AspNetCore
dotnet add package Serilog.Sinks.Elasticsearch
```

```csharp
using Serilog;
using Serilog.Sinks.Elasticsearch;

var logger = new LoggerConfiguration()
    .Enrich.FromLogContext()
    .WriteTo.Console() // Optional for local debugging
    .WriteTo.Elasticsearch(new ElasticsearchSinkOptions(new Uri("http://localhost:9200")) // Use your Elasticsearch URL
    {
        AutoRegisterTemplate = true,
        IndexFormat = "myapp-logs-{0:yyyy.MM.dd}" // Daily index
    })
    .CreateLogger();

Log.Logger = logger;

// builder.Host.UseSerilog();
```

### Azure Application Insights

- Logs are sent to Azure Application Insights for monitoring and analysis.
- Cloud or telemetry monitoring.
- Provider: `Microsoft.ApplicationInsights.AspNetCore`
- Azure Application Insights is a cloud-based application performance management (APM) and monitoring service provided by Microsoft as part of Azure Monitor.
- It helps diagnose issues and understand how your app is being used.

```
[Your ASP.NET Core App]
    |
    v
(Telemetry SDK)
    |
    v
[Azure Application Insights]
    |
    v
[Azure Portal: Dashboards, Logs, Alerts, Analytics]
```
