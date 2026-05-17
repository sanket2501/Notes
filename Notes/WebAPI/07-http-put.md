---
title: HTTP PUT
source_filename: 7. HTTP PUT.md
created: 2026-05-17
tags:
  - notes
  - converted
  - dotnet
languages:
  - csharp
---

# HTTP PUT

- The **PUT** method in RESTful APIs is used to update an existing resource or create it if it does not exist.
- When sending a PUT request, the expectation is that the resource will be replaced with the data provided in the request body.

```csharp
[Route("api/[controller]")]
[ApiController]
public class ProductsController : ControllerBase
{
    private readonly IProductRepository _repository;

    public ProductsController(IProductRepository repository)
    {
        _repository = repository;
    }

    // PUT api/products/5
    [HttpPut("{id}")]
    public IActionResult UpdateProduct(int id, [FromBody] Product updatedProduct)
    {
        if (updatedProduct == null)
        {
            return BadRequest("Invalid product data.");
        }

        var existingProduct = _repository.GetProductById(id);

        if (existingProduct == null)
        {
            return NotFound();
        }

        // Replace the existing product's details
        existingProduct.Name = updatedProduct.Name;
        existingProduct.Price = updatedProduct.Price;

        _repository.UpdateProduct(existingProduct);
        return NoContent(); // Status 204 (No Content), the resource is updated successfully.
    }
}
```

- **Full Replacement**: A PUT request typically expects the entire resource, not just changed fields. In the example above, both `Name` and `Price` are updated. Missing fields may be overwritten depending on implementation.
- **Partial Updates**: For updating only some fields, consider using `PATCH` instead of `PUT`, because PATCH is meant for partial updates.
- **Idempotency**: `PUT` is idempotent, meaning repeated identical requests should produce the same result every time.
