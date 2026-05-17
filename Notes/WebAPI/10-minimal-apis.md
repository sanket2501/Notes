---
title: Minimal APIs
source_filename: 10. Minimal APIs.md
created: 2026-05-17
tags:
  - notes
  - converted
  - dotnet
languages:
  - csharp
---

# Minimal APIs

- In .NET 6, a new feature called **Minimal APIs** was introduced. This allows creating APIs with minimal code.
- Minimal APIs are suited for microservices and apps that want to include the fewest files, features, and dependencies.
- Minimal APIs are architected to create HTTP APIs with minimal dependencies.
- When creating a minimal API, uncheck the `Use controllers` option during project setup.
- After the project is created, the `Controllers` folder will typically not exist.
- Actions that would normally go in controllers are coded directly in `Program.cs`.

```csharp
public class Product
{
    public string Name { get; set; }
    public decimal Price { get; set; }
}

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Create (POST)
app.MapPost("/products", (string product) =>
{
    products.Add(product);
    return Results.Created($"/products/{product}", product);
});

// Read (GET)
app.MapGet("/products", () => Results.Ok(products));

// POST request with body model binding
app.MapPost("/products", (Product product) =>
{
    // Do something with the product (e.g., save to a database)
    return Results.Created($"/products/{product.Name}", product);
});

// Asynchronous GET request to simulate a long-running task
app.MapGet("/async-task", async () =>
{
    // Simulating a delay (e.g., fetching from a database or external API)
    await Task.Delay(2000);
    return Results.Ok("Data fetched successfully after delay.");
});

// Add authentication
builder.Services.AddAuthentication("Bearer")
    .AddJwtBearer(options =>
    {
        options.Authority = "https://your-identity-server";
        options.Audience = "api";
    });

// Protect the endpoint with authentication
app.MapGet("/protected", [Authorize] () => "This is a protected resource.")
    .RequireAuthorization();

app.Run();
```
