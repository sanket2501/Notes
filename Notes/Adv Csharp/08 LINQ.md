# 08. LINQ

Tags: dotnet

LINQ stands for Language Integrated Query. It allows developers to query and transform collections using C# syntax instead of writing manual loops. It is widely used in .NET for filtering, sorting, grouping, and projecting data from lists, arrays, XML, and database providers.

## Table of Contents

- [What is LINQ](#what-is-linq)
- [LINQ query syntax](#linq-query-syntax)
- [LINQ method syntax](#linq-method-syntax)
- [Deferred execution](#deferred-execution)
- [Common LINQ operators](#common-linq-operators)
- [Real-world example](#real-world-example)

## What is LINQ

LINQ integrates query capabilities directly into C#.

```csharp
var numbers = new List<int> { 10, 20, 30, 40 };
var result = numbers.Where(n => n > 20).Select(n => n * 2).ToList();

Console.WriteLine(string.Join(", ", result));
```

This is cleaner and more expressive than manually iterating through collections.

## LINQ query syntax

```csharp
var products = new List<Product>
{
    new() { Name = "Laptop", Price = 1200 },
    new() { Name = "Phone", Price = 800 },
    new() { Name = "Tablet", Price = 500 }
};

var expensiveProducts =
    from p in products
    where p.Price > 700
    orderby p.Price descending
    select p;

foreach (var product in expensiveProducts)
{
    Console.WriteLine(product.Name);
}
```

## LINQ method syntax

```csharp
var names = new List<string> { "Amit", "Riya", "John", "Sara" };
var filtered = names.Where(n => n.StartsWith("A")).ToList();
var ordered = names.OrderBy(n => n).ToList();
```

Common operators:

- `Where()` – filters data
- `Select()` – projects data
- `OrderBy()` / `OrderByDescending()` – sorts data
- `GroupBy()` – groups data
- `FirstOrDefault()` – gets first item or default
- `Sum()` / `Count()` – aggregate calculations

## Deferred execution

LINQ queries are typically executed when the sequence is enumerated, not necessarily when the query is created.

```csharp
var numbers = new List<int> { 1, 2, 3, 4 };
var query = numbers.Where(n => n > 2);

numbers.Add(10);

foreach (var item in query)
{
    Console.WriteLine(item);
}
```

This behavior is useful when the source collection changes later.

## Common LINQ operators

```csharp
var employees = new List<Employee>
{
    new() { Name = "Amit", Department = "IT", Salary = 80000 },
    new() { Name = "Riya", Department = "HR", Salary = 60000 },
    new() { Name = "Rahul", Department = "IT", Salary = 90000 }
};

var itEmployees = employees.Where(e => e.Department == "IT");
var totalSalary = employees.Sum(e => e.Salary);
var avgSalary = employees.Average(e => e.Salary);
var byDept = employees.GroupBy(e => e.Department);
```

## Real-world example

### Example: filtering products for a storefront

```csharp
public class Product
{
    public string Name { get; set; }
    public decimal Price { get; set; }
    public string Category { get; set; }
}

var products = new List<Product>
{
    new() { Name = "Laptop", Price = 1200, Category = "Electronics" },
    new() { Name = "Phone", Price = 800, Category = "Electronics" },
    new() { Name = "Chair", Price = 200, Category = "Furniture" }
};

var affordableElectronics = products
    .Where(p => p.Category == "Electronics" && p.Price < 1000)
    .OrderBy(p => p.Price)
    .Select(p => p.Name)
    .ToList();

foreach (var item in affordableElectronics)
{
    Console.WriteLine(item);
}
```

## Best practices

- Use LINQ for querying and transformation, not for complex side effects.
- Prefer readable queries for business logic.
- Keep operations composable and clear.
- Be aware of deferred execution when the data source changes.

## Summary

LINQ makes C# code expressive and concise for collection processing. In real applications, it is used heavily for filtering data, building reports, and transforming domain objects before they are sent to UI or API layers.
