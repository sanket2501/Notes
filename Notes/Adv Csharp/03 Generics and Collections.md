# 03. Generics and Collections

Tags: dotnet

Generics allow you to define reusable, type-safe code. Instead of writing separate logic for `int`, `string`, or custom objects, you can create one class or method that works with any type. This improves performance, reduces duplication, and makes the code safer at compile time.

## Table of Contents

- [Why generics matter](#why-generics-matter)
- [Generic classes and methods](#generic-classes-and-methods)
- [Generic interfaces and delegates](#generic-interfaces-and-delegates)
- [Generic collections](#generic-collections)
- [Non-generic collections](#non-generic-collections)
- [Common collection operations](#common-collection-operations)
- [Real-world examples](#real-world-examples)

## Why generics matter

Benefits include:

- Type safety at compile time
- Better performance than object-based code
- Less boxing/unboxing for value types
- Reusable business logic

Example of object-based code with boxing:

```csharp
ArrayList items = new();
items.Add(10);
items.Add("hello");

int x = (int)items[0];
```

This is unsafe because any type can be inserted. With generics:

```csharp
List<int> numbers = new() { 10, 20, 30 };
int x = numbers[0];
```

## Generic classes and methods

```csharp
public class GenericList<T>
{
    private readonly List<T> _items = new();

    public void Add(T item)
    {
        _items.Add(item);
    }

    public T Get(int index)
    {
        return _items[index];
    }
}

public class Utility
{
    public static void Swap<T>(ref T first, ref T second)
    {
        T temp = first;
        first = second;
        second = temp;
    }
}
```

Usage:

```csharp
var numbers = new GenericList<int>();
numbers.Add(10);
Console.WriteLine(numbers.Get(0));

int a = 5, b = 7;
Utility.Swap(ref a, ref b);
Console.WriteLine($"a={a}, b={b}");
```

## Generic interfaces and delegates

```csharp
public interface IRepository<T>
{
    void Add(T item);
    T GetById(int id);
}

public delegate T Transformer<T>(T input);
```

A common real-world use is a repository layer that works with different entity types.

```csharp
public class ProductRepository : IRepository<Product>
{
    public void Add(Product item)
    {
        Console.WriteLine($"Added product {item.Name}");
    }

    public Product GetById(int id)
    {
        return new Product { Id = id, Name = "Sample" };
    }
}
```

## Generic collections

These collections enforce type safety.

- `List<T>`: dynamic array
- `Dictionary<TKey,TValue>`: key-value lookups
- `Queue<T>`: FIFO collection
- `Stack<T>`: LIFO collection
- `HashSet<T>`: unique values only

```csharp
var customers = new List<string> { "Amit", "Rahul", "Sara" };
var ages = new Dictionary<string, int>
{
    ["Amit"] = 34,
    ["Rahul"] = 29
};

var queue = new Queue<string>();
queue.Enqueue("Order 1");
queue.Enqueue("Order 2");

var stack = new Stack<int>();
stack.Push(10);
stack.Push(20);
```

## Non-generic collections

These are older, less safe collections that can store any object type.

```csharp
using System.Collections;

ArrayList values = new();
values.Add(10);
values.Add("hello");
values.Add(true);

Hashtable cache = new();
cache["userId"] = 101;
cache["status"] = "active";
```

These are not recommended in modern .NET code because they require casting and increase runtime errors.

## Common collection operations

```csharp
var numbers = new List<int> { 3, 1, 2, 4 };

numbers.Add(5);
numbers.Remove(2);
numbers.RemoveAt(0);

bool hasThree = numbers.Contains(3);
var sorted = numbers.OrderBy(x => x).ToList();

var names = new List<string> { "Amit", "Bharat", "Chetan" };
var selected = names.Where(n => n.StartsWith("B")).ToList();
```

Common operations include:

- `Add()`: add item
- `Remove()`: remove specific item
- `RemoveAt()`: remove by index
- `Contains()`: check membership
- `Sort()`: sort elements
- initialization with collection literals

## Real-world examples

### Example 1: Shopping cart

```csharp
public class CartItem
{
    public string ProductName { get; set; }
    public decimal Price { get; set; }
}

public class ShoppingCart
{
    private readonly List<CartItem> _items = new();

    public void AddItem(CartItem item)
    {
        _items.Add(item);
    }

    public decimal GetTotal()
    {
        return _items.Sum(i => i.Price);
    }
}
```

### Example 2: user cache

```csharp
var userCache = new Dictionary<int, string>
{
    [101] = "Amit",
    [102] = "Riya"
};

Console.WriteLine(userCache[101]);
```

## Summary

Generics and collections are core to advanced .NET development. They help you write clean, fast, and safe code for data-heavy applications, from product inventories and caches to API responses and in-memory processing.
