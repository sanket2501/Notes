# 01. Serilog

**Serilog** is a popular .NET logging library built for **structured logging**. Instead of writing plain text messages, it captures logs as strongly structured events with properties, making them easier to search, analyze, and store in tools like Elasticsearch, SQL Server, Seq, or application insights.

## Why use Serilog?

1. **Structured logging**: log data as key-value pairs instead of plain text.
2. **Rich filtering**: easy to filter logs by level, property, and message.
3. **Multiple sinks**: send logs to console, file, database, Seq, Application Insights, etc.
4. **Better debugging**: logs include meaningful metadata like request ID, username, exception details, and correlation data.
5. **Works smoothly with ASP.NET Core**: easy integration with dependency injection and hosting.

## Key concepts in Serilog

### Logger
A logger is the main object used to write log entries.

### Log event
A log event contains:

- message
- log level
- timestamp
- properties
- exception information

### Log level
Common log levels are:

- Verbose / Debug
- Information
- Warning
- Error
- Fatal

### Sink
A sink is a destination where logs are written. Examples:

- Console
- File
- Seq
- SQL Server
- Application Insights
- Elasticsearch

### Enrichers
Enrichers add extra properties into each log event. Common examples:

- machine name
- environment name
- application version
- request ID
- user name

### Minimum level
You can define the minimum log level that should be written.

Example:

- Information: logs info, warnings, errors, fatal
- Warning: ignores info logs
- Error: only error and fatal

## Installing Serilog

For ASP.NET Core projects, install these packages:

```bash
dotnet add package Serilog.AspNetCore
```

Additional suggested packages:

```bash
dotnet add package Serilog.Settings.Configuration
dotnet add package Serilog.Sinks.Console
dotnet add package Serilog.Sinks.File
dotnet add package Serilog.Enrichers.Environment
dotnet add package Serilog.Enrichers.Process
dotnet add package Serilog.Enrichers.Thread
dotnet add package Serilog.Sinks.Seq
```

## Basic implementation in ASP.NET Core

### Step 1: Configure Serilog in Program.cs

```csharp
using Serilog;

var builder = WebApplication.CreateBuilder(args);

Log.Logger = new LoggerConfiguration()
    .ReadFrom.Configuration(builder.Configuration)
    .Enrich.FromLogContext()
    .WriteTo.Console()
    .CreateLogger();

builder.Host.UseSerilog();

var app = builder.Build();

app.MapGet("/", () => "Hello from Serilog!");

app.Run();
```

### Step 2: Add config in appsettings.json

```json
{
  "Serilog": {
    "Using": [ "Serilog.Sinks.Console", "Serilog.Sinks.File" ],
    "MinimumLevel": {
      "Default": "Information",
      "Override": {
        "Microsoft": "Warning",
        "System": "Warning"
      }
    },
    "WriteTo": [
      { "Name": "Console" },
      {
        "Name": "File",
        "Args": {
          "path": "Logs/log-.txt",
          "rollingInterval": "Day"
        }
      }
    ],
    "Enrich": [ "FromLogContext", "WithMachineName", "WithThreadId" ],
    "Properties": {
      "Application": "MyApp"
    }
  }
}
```

### Step 3: Use logger in controllers or services

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("[controller]")]
public class WeatherController : ControllerBase
{
    private readonly ILogger<WeatherController> _logger;

    public WeatherController(ILogger<WeatherController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public IActionResult Get()
    {
        _logger.LogInformation("Weather endpoint called");
        _logger.LogWarning("This is a warning example");

        return Ok(new { message = "Success" });
    }
}
```

## Structured logging example

Instead of:

```csharp
logger.LogInformation("User {UserId} logged in", userId);
```

You can write:

```csharp
logger.LogInformation("User logged in. UserId: {UserId}, Email: {Email}, Role: {Role}",
    userId,
    email,
    role);
```

This produces structured data that is easier to query in log tools.

## Logging exceptions

```csharp
try
{
    // business logic
}
catch (Exception ex)
{
    Log.Error(ex, "An error occurred while processing order {OrderId}", orderId);
}
```

You can also log exceptions through injected logger:

```csharp
_logger.LogError(ex, "Failed to create order for customer {CustomerId}", customerId);
```

## Logging scopes and context

Serilog supports logging contextual properties using `LogContext`.

```csharp
using Serilog.Context;

public class OrderService
{
    public void CreateOrder(int customerId)
    {
        using (LogContext.PushProperty("CustomerId", customerId))
        {
            Log.Information("Creating order for customer");
        }
    }
}
```

This is useful when you want all logs in a request or operation to include a common property.

## Middleware example for request logging

```csharp
app.Use(async (context, next) =>
{
    using (Serilog.Context.LogContext.PushProperty("RequestId", context.TraceIdentifier))
    {
        Log.Information("Request started: {Method} {Path}",
            context.Request.Method,
            context.Request.Path);

        await next();

        Log.Information("Request ended: {Method} {Path} status {StatusCode}",
            context.Request.Method,
            context.Request.Path,
            context.Response.StatusCode);
    }
});
```

## Sinks

### Console sink

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.Console()
    .CreateLogger();
```

### File sink

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.File("logs/myapp.txt", rollingInterval: RollingInterval.Day)
    .CreateLogger();
```

### Seq sink

Install package:

```bash
dotnet add package Serilog.Sinks.Seq
```

Configuration:

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.Seq("http://localhost:5341")
    .CreateLogger();
```

Seq is useful when you want a powerful log dashboard with search and filtering.

## Best practices

1. Use **structured logging** instead of string concatenation.
2. Log at the correct level.
3. Avoid logging sensitive data like passwords or tokens.
4. Include meaningful context such as user IDs, request IDs, and correlation IDs.
5. Use `ILogger<T>` in ASP.NET Core classes and avoid static logging unless necessary.
6. Keep configuration in `appsettings.json` for easier environment-specific setups.
7. Store logs in external sinks for production environments.

## Common configuration patterns

### Setting minimum level programmatically

```csharp
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Information()
    .WriteTo.Console()
    .CreateLogger();
```

### Reading from appsettings

```csharp
Log.Logger = new LoggerConfiguration()
    .ReadFrom.Configuration(builder.Configuration)
    .CreateLogger();
```

### Filtering logs by source

```csharp
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Information()
    .Filter.ByExcluding(logEvent => logEvent.Properties.ContainsKey("SourceContext") &&
        logEvent.Properties["SourceContext"].ToString().Contains("HealthCheck"))
    .WriteTo.Console()
    .CreateLogger();
```

## Example: complete ASP.NET Core setup

```csharp
using Serilog;

var builder = WebApplication.CreateBuilder(args);

builder.Host.UseSerilog((context, services, loggerConfiguration) =>
{
    loggerConfiguration
        .ReadFrom.Configuration(context.Configuration)
        .Enrich.FromLogContext()
        .WriteTo.Console()
        .WriteTo.File("Logs/log-.txt", rollingInterval: RollingInterval.Day);
});

var app = builder.Build();

app.MapGet("/health", () => Results.Ok(new { status = "Healthy" }));

app.MapGet("/products/{id}", (int id, ILoggerFactory loggerFactory) =>
{
    var logger = loggerFactory.CreateLogger("Products");
    logger.LogInformation("Fetching product {ProductId}", id);
    return Results.Ok(new { ProductId = id, Name = "Laptop" });
});

app.Run();
```

## Benefits of Serilog in real projects

1. Helps trace issues in production systems.
2. Makes logs useful for monitoring and debugging.
3. Integrates well with cloud-native and enterprise apps.
4. Supports compliance and auditing by capturing structured event data.
5. Makes log analysis much easier than plain text logs.

## Summary

Serilog is one of the best logging libraries for .NET because it is:

- easy to integrate with ASP.NET Core
- powerful for structured logging
- flexible with multiple sinks
- highly useful for debugging and monitoring production systems

In most real-world applications, Serilog is used with:

- `Console` for local development
- `File` for persistence
- `Seq` or cloud monitoring for production analysis

## Quick example

```csharp
Log.Information("Application started at {StartTime}", DateTime.UtcNow);
Log.Warning("User {UserId} tried to access an unauthorized resource", 101);
Log.Error("Processing failed for order {OrderId}", 5001);
```

This simple pattern is enough to make your logs readable, structured, and much more powerful.
