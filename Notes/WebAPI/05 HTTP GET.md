# 05. HTTP GET

- The **HTTP GET** method is used to fetch data from the server — e.g., retrieving a list of resources or a specific resource.
- In an API controller, an action annotated with **`[HttpGet]`** handles GET requests.

## Routing & controller attributes

- **`[Route("api/[controller]")]`**: defines the route template for the controller; the token `[controller]` is replaced by the controller name (without the `Controller` suffix).
- **`[ApiController]`**: enables API-friendly behavior such as automatic model binding/validation and consistent error responses.

## Example: ProductsController

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace MyApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ProductsController : ControllerBase
    {
        // Example in-memory list
        private static readonly List<Product> products = new()
        {
            new Product { Id = 1, Name = "Keyboard" },
            new Product { Id = 2, Name = "Mouse" }
        };

        // GET: api/products
        [HttpGet]
        public IActionResult GetProducts()
        {
            return Ok(products); // 200 OK with JSON body
        }

        // GET: api/products/{id}
        [HttpGet("{id}")]
        public IActionResult GetProduct(int id)
        {
            var product = products.Find(p => p.Id == id);
            if (product == null) return NotFound(); // 404
            return Ok(product);
        }
    }

    // Simple model for example
    public record Product { public int Id { get; init; } public string Name { get; init; } }
}
```

## Using filters with `HttpGet`

1. **Action filters** let you run logic before or after an action executes (e.g., logging, metrics, authorization checks).

Example logging filter:

```csharp
using Microsoft.AspNetCore.Mvc.Filters;

public class LoggingActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        // Custom logic before the action executes (e.g., log request)
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // Custom logic after the action executes (e.g., log response)
    }
}
```

Register and apply the filter to an action or controller:

```csharp
[ServiceFilter(typeof(LoggingActionFilter))]
[HttpGet]
public IActionResult GetProducts() => Ok(products);
```

> Tip: register filters with DI in `Program.cs` (e.g., `builder.Services.AddScoped<LoggingActionFilter>();`).

## HTTP GET with caching

- Caching GET responses reduces server load and improves performance. You can use middleware or response caching attributes.

Example: attribute-based caching for 60 seconds:

```csharp
[HttpGet]
[ResponseCache(Duration = 60)] // Cache response for 60 seconds
public IActionResult GetProducts()
{
    return Ok(products);
}
```

Notes:
- Use caching carefully — only cache safe, idempotent GET responses.
- For distributed caching or advanced scenarios, consider using `ResponseCaching` middleware with proper cache-control headers or a CDN.

--- 
