# 04. Var and Dynamic

Tags: dotnet

C# provides multiple ways to declare variables based on how much type safety you want at compile time. The two commonly discussed keywords are `var` and `dynamic`. They solve different problems and should be used intentionally.

## Table of Contents

- [Implicitly typed local variables](#implicitly-typed-local-variables)
- [Dynamic variables](#dynamic-variables)
- [When to use `var`](#when-to-use-var)
- [When to use `dynamic`](#when-to-use-dynamic)
- [Real-world examples](#real-world-examples)

## Implicitly typed local variables

The `var` keyword lets the compiler infer the variable type.

```csharp
var number = 10; // inferred as int
var name = "Amit"; // inferred as string
var isActive = true; // inferred as bool
```

Important rules:

- `var` is still strongly typed.
- The type is determined at compile time.
- It cannot change later.

```csharp
var value = 100;
// value = "abc"; // compile-time error
```

This is very useful when the type is obvious, especially with LINQ queries or complex objects.

## Dynamic variables

The `dynamic` keyword delays type checking until runtime. This gives more flexibility but also moves errors from compile time to runtime.

```csharp
dynamic value = 1;
Console.WriteLine(value + 2); // works

value = "Hello";
Console.WriteLine(value.ToUpper()); // works
```

If the operation is invalid for the runtime type, an exception is thrown.

```csharp
dynamic data = 10;
// Console.WriteLine(data.Length); // runtime error, because int has no Length
```

## When to use `var`

Use `var` when:

- The type is obvious from the initializer
- The code is clear and readable
- You are working with LINQ or object initialization

Example:

```csharp
var customers = new List<Customer>
{
    new() { Id = 1, Name = "Amit" },
    new() { Id = 2, Name = "Riya" }
};
```

This is better than writing `List<Customer>` repeatedly when the type is obvious.

## When to use `dynamic`

Use `dynamic` when:

- you are working with COM interop
- you use reflection-heavy or runtime-bound APIs
- you need runtime flexibility

Example with a dynamic object:

```csharp
dynamic json = new System.Text.Json.JsonDocument();
```

In practice, developers usually prefer `var` in most application code because it is safer and easier to refactor.

## Real-world examples

### Example 1: API result model

```csharp
var result = new
{
    UserId = 101,
    Name = "Amit",
    Email = "amit@example.com"
};

Console.WriteLine(result.Name);
```

This is common in anonymous types.

### Example 2: Dynamic data processing

```csharp
public class DynamicProcessor
{
    public void Process(dynamic input)
    {
        Console.WriteLine(input.GetType().Name);
        Console.WriteLine(input.ToString());
    }
}

var processor = new DynamicProcessor();
processor.Process(123);
processor.Process("hello");
processor.Process(DateTime.Now);
```

### Example 3: Safer typed alternative

```csharp
var orderTotal = 2500.50m;
Console.WriteLine(orderTotal.GetType().Name);
```

Here `var` is still strongly typed as `decimal`.

## Best practices

- Prefer `var` for local variables when the type is clear.
- Avoid `dynamic` unless runtime flexibility is genuinely needed.
- Do not use `dynamic` where compile-time checking is important.
- Keep code readable and consistent across the team.

## Summary

`var` is a compile-time convenience that preserves type safety. `dynamic` is a runtime flexibility feature that can be powerful but more error-prone. In modern C# code, `var` is usually the safer default.
