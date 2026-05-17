---
title: HTTP DELETE
source_filename: 8. HTTP DELETE.md
created: 2026-05-17
tags:
  - notes
  - converted
  - dotnet
languages:
  - csharp
---

# HTTP DELETE

- The **DELETE** operation in ASP.NET Core Web API is used to remove a resource from the server.

```csharp
public class ProductsController : ControllerBase
{
    private readonly ApplicationDbContext _context;

    public ProductsController(ApplicationDbContext context)
    {
        _context = context;
    }

    // DELETE api/products/{id}
    [HttpDelete("{id}")]
    public async Task<IActionResult> DeleteProduct(int id)
    {
        // Find the product in the database
        var product = await _context.Products.FindAsync(id);
        
        // If the product doesn't exist, return a 404 (Not Found) response
        if (product == null)
        {
            return NotFound(new { message = "Product not found" });
        }

        // Remove the product from the database
        _context.Products.Remove(product);

        // Save changes to the database
        await _context.SaveChangesAsync();

        // Return a 204 No Content response to indicate successful deletion
        return NoContent();
    }
}
```

- **Soft Deletes**: Instead of permanently deleting a record, you may choose to perform a soft delete by setting a `Deleted` flag in the database. This helps preserve audit logs and reduces accidental data loss.
