# 04. Controller

- A Controller is a class placed under the `Controllers` folder. It handles incoming HTTP requests and returns responses to the caller.
- Methods in a Controller are called **Actions**. Actions can return several types:
    - Specific types (e.g., `void`, `int`, `bool`, `JsonResult`)
    - **`ActionResult`**
    - **`IActionResult`**

## Common return types for controller actions

1. **`IActionResult`** — Flexible return type allowing you to return various status codes and data types.
2. **`ActionResult<T>`** — Strongly typed return type combining flexibility and type safety.
3. **Asynchronous variants**: `Task<IActionResult>` or `Task<ActionResult<T>>` when performing async work (database calls, I/O).

Example (async GET returning `ActionResult<Person>`):

```csharp
[HttpGet]
public async Task<ActionResult<Person>> GetPersonAsync(int id)
{
    var person = await _context.Persons.FindAsync(id);

    if (person == null)
    {
        return NotFound();
    }

    return Ok(person);
}
```

4. **`IEnumerable<T>`** or **`IQueryable<T>`** — Used for returning collections of data.
5. **`NoContentResult`** — Common for DELETE operations when no content is returned (HTTP 204).

Example (DELETE returning 204 No Content):

```csharp
[HttpDelete("{id}")]
public IActionResult DeletePerson(int id)
{
    var person = _context.Persons.Find(id);
    if (person == null)
    {
        return NotFound();
    }

    _context.Persons.Remove(person);
    _context.SaveChanges();
    return NoContent(); // 204 No Content
}
```

6. **`CreatedAtAction`** — For POST operations when a resource is created (HTTP 201).

Example (POST returning CreatedAtAction):

```csharp
[HttpPost]
public ActionResult<Person> CreatePerson(Person person)
{
    _context.Persons.Add(person);
    _context.SaveChanges();

    return CreatedAtAction(nameof(GetPerson), new { id = person.Id }, person);
}
```

7. **`RedirectToAction`** — Redirects to another action (optionally in a different controller).
8. **`RedirectToActionPermanent`** — Sends a 301 Moved Permanently redirect.
9. **`RedirectToRoute`** — Redirects to a named route.
10. **`Redirect`** — Redirects to a specific URL (absolute or relative).
11. **`RedirectResult`** — Returns a 302 Found temporary redirect to a specified URL.

---
