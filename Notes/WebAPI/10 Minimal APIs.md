# 10. Minimal APIs

- In .NET 6, a new feature of **Minimal APIs** has been introduced. This allows us to create an API with minimum code.
- Minimal APIs are microservices and apps that want to include minimum files, features, and dependencies.
- Minimal APIs are architected to create HTTP APIs with minimal dependencies.
- You can create a minimal API following the same steps as you created for controller API in the previous demos, the only difference is that during the creation the option `Use controllers` is to be unchecked as shown in the below diagram.
- Once a minimal API project is created, you will see that the `Controllers` folder will not be existing.
- All the actions which were mentioned in the controller are now to be coded at `Program.cs`.

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
