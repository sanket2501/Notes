# 11. Routing

- Conventional routing (also known as standard routing) is the default approach to route HTTP requests to controllers and actions.

1. Defining Routes in `Program.cs` (or `Startup.cs`)

```csharp
var builder = WebApplication.CreateBuilder(args);

// Register services
builder.Services.AddControllersWithViews();  // Add support for MVC Controllers and Views

var app = builder.Build();

// Set up routing
app.UseRouting();  // Enable routing middleware

app.MapControllerRoute(
    name: "product",
    pattern: "Products/{id}",
    defaults: new { controller = "Product", action = "Details" }
);

app.Run();
```

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        return View();
    }

    public IActionResult About()
    {
        return View();
    }
}
```

- Attribute routing in ASP.NET Core is a more flexible and fine-grained approach to defining routes compared to conventional routing.
- With attribute routing, you specify the routes directly on controller actions using attributes.

## How to Set Up Attribute Routing

- Enable Attribute Routing: In ASP.NET Core, you need to enable attribute routing in the `Program.cs` (or `Startup.cs` for older versions) by adding `AddControllers()` or `AddControllersWithViews()`.

```csharp
var builder = WebApplication.CreateBuilder(args);

// Enable attribute routing by adding controllers
builder.Services.AddControllers();

var app = builder.Build();

app.UseRouting();

app.MapControllers();  // Enables attribute routing

app.Run();
```

```csharp
[Route("products")]
public class ProductController : Controller
{
    // Matches: GET /products
    [HttpGet]
    public IActionResult Index()
    {
        return Ok("All products");
    }

    // Matches: GET /products/5
    [HttpGet("{id}")]
    public IActionResult GetById(int id)
    {
        return Ok($"Product {id}");
    }

    // Matches: GET /products/search?category=electronics
    [HttpGet("search")]
    public IActionResult Search(string category)
    {
        return Ok($"Search products in category: {category}");
    }
   
    [HttpGet("{id?}")]
    public IActionResult GetById(int? id)
    {
        return Ok($"Product {id}");
    }

    // Matches: GET /products/5 (id must be an integer)
    [HttpGet("{id:int}")]
    public IActionResult GetById(int id)
    {
        return Ok($"Product {id}");
    }
}
```
