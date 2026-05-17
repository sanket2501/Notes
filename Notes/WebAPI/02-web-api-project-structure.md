---
title: Web API Project Structure
source_filename: 2. Web API Project Structure.md
created: 2026-05-17
tags:
  - notes
  - converted
  - dotnet
languages:
  - csharp
---

# Web API Project Structure

```
MyWebApi/
│
├── Controllers/                  # API controllers (business logic for routes)
│   ├── WeatherForecastController.cs
│   └── HelloController.cs
│
├── Models/                       # Data models (POCO classes for requests/responses)
│   ├── WeatherForecast.cs
│   └── Person.cs
│
├── Data/                         # Database-related classes like DbContext
│   └── ApplicationDbContext.cs
│
├── Services/                     # Business logic and services
│   └── PersonService.cs
│
├── wwwroot/                      # Static files (HTML, CSS, JS, etc.)
│   ├── css/
│   ├── js/
│   └── images/
│
├── appsettings.json            # App configuration settings
├── Program.cs                    # Main configuration & setup
└── MyWebApi.csproj         # Project file (dependencies, settings)
```

## Program.cs

1. This is the entry point for the application and is responsible for configuring the app’s services and the request pipeline. In .NET 6 and later, `Program.cs` combines the functionality of both `Startup.cs` and `Program.cs` from previous versions.
2. This is where you set up services like database connections, authentication, and routing, as well as configure the middleware pipeline.

Key points:

- `WebApplication.CreateBuilder()` initializes `WebApplicationBuilder` with defaults; use the returned `builder` to configure services.
- `builder.Services.AddControllers()` registers controllers, model binding and API exploration.
- `builder.Services.AddEndpointsApiExplorer()` enables OpenAPI endpoint discovery.
- `builder.Services.AddSwaggerGen()` generates Swagger/OpenAPI documentation.

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers(); // Register services to the container.
builder.Services.AddEndpointsApiExplorer(); // For OpenAPI (Swagger)
builder.Services.AddSwaggerGen(); // Swagger UI

// Optionally, add a DbContext for Entity Framework Core (Example)
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.MapControllers(); // Maps controllers to routes

app.Run();
```

## Configuration files

- `appsettings.json`
  1. Store configuration settings for your application (e.g., database connection strings, API keys).
  2. Typical keys include `ConnectionStrings`, `Logging`, and `AllowedHosts`.

- `appsettings.Development.json` — optional file for development-specific settings.
- `Properties/launchSettings.json` — environment settings for running the app locally (ports, browser launch options).

## Folders and responsibilities

- `Controllers/`
  1. Contains API controller classes. Controllers handle HTTP requests and return responses.
  2. Each controller typically contains action methods corresponding to HTTP verbs (GET, POST, PUT, DELETE, PATCH).

- `Models/`
  1. Define the data structures used by your API (POCOs).

- `Data/`
  1. Manage database-related logic. Often contains a `DbContext` for Entity Framework Core.
  2. Example `ApplicationDbContext` below.

```csharp
using Microsoft.EntityFrameworkCore;

namespace MyWebApi.Data {
    public class ApplicationDbContext : DbContext {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options) { }
        public DbSet<Person> Persons { get; set; }
    }
}
```

- `Services/`
  1. Place for business logic and services (used via dependency injection).

## Project dependencies

- `MyWebApi.csproj` contains project dependencies such as Entity Framework Core, Swashbuckle (Swagger), Newtonsoft.Json, logging libraries (Serilog/NLog), and other third-party packages.

## Migrations

- A `Migrations/` folder will be created by EF Core to store schema changes over time when you use migrations.

---

If you'd like, I can also:

- Add examples for a sample controller (`WeatherForecastController.cs`).
- Create a minimal runnable sample project scaffold.
