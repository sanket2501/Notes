# 06. HTTP POST

- The **HTTP POST** method is typically used to create new resources or perform operations that change server state (e.g., adding a record to a database, submitting a form).

## Common attributes for POST actions

1. **`[HttpPost]`**
   - Purpose: Marks an action method to handle HTTP POST requests.
   - Usage: Apply at the method level to indicate this method handles POST.

2. **`[FromBody]`**
   - Purpose: Bind a parameter from the request body (usually JSON or XML).
   - Usage: Use for accepting complex types.

```csharp
public class Item
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}

[HttpPost]
public IActionResult CreateItem([FromBody] Item item)
{
    if (item == null)
    {
        return BadRequest("Item is null.");
    }

    // Persist the item (example)
    // _context.Items.Add(item);
    // _context.SaveChanges();

    return CreatedAtAction(nameof(GetItem), new { id = item.Id }, item); // 201 Created
}
```

3. **`[FromForm]`**
   - Purpose: Bind parameters from form data (`application/x-www-form-urlencoded` or `multipart/form-data`).
   - Usage: Typical for HTML form submissions and file uploads.

```csharp
[HttpPost]
public IActionResult SubmitForm([FromForm] string username, [FromForm] string password)
{
    // Process form data
    return Ok();
}
```

HTML form example:

```html
<form method="post" action="/api/items">
  <input type="text" name="username" />
  <input type="password" name="password" />
  <button type="submit">Submit</button>
</form>
```

4. **`[FromQuery]`**
   - Purpose: Bind parameters from the query string (`?key=value`).
   - Usage: Useful for simple values like filters or flags.

```csharp
[HttpPost]
public IActionResult FilterItems([FromQuery] string category, [FromQuery] decimal minPrice)
{
    // Filter items based on query
    return Ok();
}

// Example request: POST /api/items?category=electronics&minPrice=50
```

5. **`[FromRoute]`**
   - Purpose: Bind parameters from route segments (e.g., `/api/items/{id}`).
   - Usage: When route values are part of the URL path.

```csharp
[HttpPost("{id}")]
public IActionResult UpdateItem([FromRoute] int id, [FromBody] Item item)
{
    // Update item logic using id and item
    return Ok();
}

// Example request: POST /api/items/5
```

## Return types and status codes for POST operations

- **201 Created**: When a new resource is created successfully. Use `CreatedAtAction(...)` to return location and resource.
- **400 Bad Request**: When the request body or parameters are invalid.
- **500 Internal Server Error**: When an unexpected error occurs on the server.

```csharp
// Example: return 201 Created
return CreatedAtAction(nameof(GetItem), new { id = item.Id }, item);

// Example: return 400 Bad Request
return BadRequest("Invalid payload");

// Example: return 500 Internal Server Error
return StatusCode(StatusCodes.Status500InternalServerError, "An error occurred");
```

## Tips and best practices

- Prefer **`[FromBody]`** for complex types (DTOs) and **`[FromForm]`** for form/file submissions.
- Validate incoming models using model validation attributes and check `ModelState.IsValid` or rely on `[ApiController]` automatic validation.
- Keep POST endpoints idempotency in mind: POST is not idempotent by definition (unlike PUT), so design accordingly.
- Return `201 Created` with a `Location` header when creating resources.

---

Would you like a sample `ItemsController.cs` scaffolded under `Controllers/` using these patterns? 
