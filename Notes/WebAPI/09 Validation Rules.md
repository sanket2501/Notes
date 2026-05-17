# 09. Validation Rules

- Data annotations allow you to specify validation rules directly on your model properties (like `Required`, `StringLength`, `Range`, etc.).

1. **Required**
   - Ensures that a property is not null or empty.

```csharp
public class Product
{
    [Required]
    public string Name { get; set; }

    [Required]
    public decimal Price { get; set; }
}
```

2. **StringLength**
   - Specifies the minimum and maximum length for a string property.

```csharp
public class Product
{
    [StringLength(100, MinimumLength = 5, ErrorMessage = "Name must be between 5 and 100 characters.")]
    public string Name { get; set; }
}
```

3. **Range**
   - Validates that a numeric property falls within a specified range.

```csharp
public class Product
{
    [Range(1, 10000, ErrorMessage = "Price must be between 1 and 10,000.")]
    public decimal Price { get; set; }
}
```

4. **RegularExpression**
   - Validates that a property matches a specified regular expression pattern.

```csharp
public class User
{
    [RegularExpression(@"^[A-Za-z0-9]+$", ErrorMessage = "Username must be alphanumeric.")]
    public string Username { get; set; }
}
```

5. **EmailAddress**
   - Validates that a property contains a valid email address.

```csharp
public class User
{
    [EmailAddress(ErrorMessage = "Invalid email address.")]
    public string Email { get; set; }
}
```

6. **Compare**
   - Compares two properties to ensure they are equal (often used for password confirmation).

```csharp
public class User
{
    public string Password { get; set; }

    [Compare("Password", ErrorMessage = "Password and confirmation password do not match.")]
    public string ConfirmPassword { get; set; }
}
```

- Model validation happens automatically in ASP.NET Core when you bind a model to an action parameter.
- You can manually check if the model is valid using `ModelState.IsValid` and return error messages if needed.

```csharp
[Route("api/[controller]")]
[ApiController]
public class ProductsController : ControllerBase
{
    // POST api/products
    [HttpPost]
    public IActionResult CreateProduct([FromBody] Product product)
    {
        // Automatically validates model based on Data Annotations
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);  // Returns validation errors if any
        }

        // Normally you would save the product to the database here
        return CreatedAtAction(nameof(CreateProduct), new { id = product.Name }, product);
    }
}
```
