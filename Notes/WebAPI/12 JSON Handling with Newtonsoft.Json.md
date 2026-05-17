# 12. JSON Handling with Newtonsoft.Json

Newtonsoft.Json (also known as Json.NET) is one of the most popular libraries for working with JSON in .NET. While `System.Text.Json` is now the default in ASP.NET Core, Newtonsoft.Json offers a more feature-rich approach to handling JSON, with better customization options for serialization and deserialization.

- To use Newtonsoft.Json in an ASP.NET Core Web API project, you need to install the `Newtonsoft.Json` NuGet package.
- Configure in `Program.cs` file:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add Newtonsoft.Json for JSON serialization and deserialization
builder.Services.AddControllers()
    .AddNewtonsoftJson(options =>
    {
        options.SerializerSettings.ContractResolver = new CamelCasePropertyNamesContractResolver();
    });

var app = builder.Build();

app.Run();
```

- Serialization (Object to JSON)

To serialize an object into a JSON string, use `JsonConvert.SerializeObject()`.

```csharp
using Newtonsoft.Json;

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}

var product = new Product
{
    Id = 1,
    Name = "Laptop",
    Price = 999.99m
};

// Serialize object to JSON string
string json = JsonConvert.SerializeObject(product);
Console.WriteLine(json);
```

- Deserialization (JSON to Object)

To deserialize a JSON string into an object, use `JsonConvert.DeserializeObject<T>()`.

```csharp
string json = "{\"Id\":1,\"Name\":\"Laptop\",\"Price\":999.99}";

// Deserialize JSON string to object
Product product = JsonConvert.DeserializeObject<Product>(json);
Console.WriteLine($"Product Name: {product.Name}, Price: {product.Price}");
```

- Customizing JSON Serialization

- Ignoring Properties

```csharp
[JsonIgnore] // This will not be serialized
```

- Customizing Property Names

```csharp
[JsonProperty("product_name")]
public string Name { get; set; }
```

- Handling Null Values

```csharp
services.AddControllers()
    .AddNewtonsoftJson(options =>
    {
        options.SerializerSettings.NullValueHandling = NullValueHandling.Ignore;
    });
```
