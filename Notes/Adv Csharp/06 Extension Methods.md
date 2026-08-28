# 06. Extension Methods

Tags: dotnet

Extension methods let you add new functionality to existing types without modifying their source code. They are especially useful when you want to enrich a library or a framework type with helper behavior while keeping the original class unchanged.

## Table of Contents

- [What is an extension method](#what-is-an-extension-method)
- [Rules for extension methods](#rules-for-extension-methods)
- [Practical example](#practical-example)
- [Real-world use cases](#real-world-use-cases)
- [Best practices](#best-practices)

## What is an extension method

An extension method is a static method defined in a static class, and the first parameter uses the `this` keyword.

```csharp
public static class StringExtensions
{
    public static bool IsEmail(this string value)
    {
        return value.Contains("@") && value.Contains(".");
    }
}

public class Program
{
    public static void Main()
    {
        string email = "amit@example.com";
        Console.WriteLine(email.IsEmail());
    }
}
```

This makes the method appear like a normal instance method on strings.

## Rules for extension methods

- They must be declared in a static class.
- They must be declared as static methods.
- The first parameter must include the `this` modifier.
- They are only available when the namespace is imported.

Example:

```csharp
public static class ListExtensions
{
    public static List<int> GetEvenNumbers(this List<int> numbers)
    {
        return numbers.Where(n => n % 2 == 0).ToList();
    }
}
```

Usage:

```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
var evens = numbers.GetEvenNumbers();
```

## Practical example

A common application is formatting and validation helper logic.

```csharp
public static class ValidationExtensions
{
    public static bool IsNullOrEmpty(this string value)
    {
        return string.IsNullOrWhiteSpace(value);
    }

    public static string ToTitleCase(this string value)
    {
        if (string.IsNullOrWhiteSpace(value)) return value;
        return char.ToUpper(value[0]) + value.Substring(1);
    }
}

public class Program
{
    public static void Main()
    {
        string name = "amit";
        Console.WriteLine(name.ToTitleCase());

        string emptyName = "";
        Console.WriteLine(emptyName.IsNullOrEmpty());
    }
}
```

## Real-world use cases

### Example 1: formatting dates

```csharp
public static class DateExtensions
{
    public static string ToFriendlyDate(this DateTime date)
    {
        return date.ToString("ddd, dd MMM yyyy");
    }
}

var today = DateTime.Today;
Console.WriteLine(today.ToFriendlyDate());
```

### Example 2: API response helpers

```csharp
public static class ApiExtensions
{
    public static string ToJsonSafe(this string value)
    {
        return value?.Replace("\"", "\\\"") ?? string.Empty;
    }
}
```

This is handy in systems that build custom JSON strings or sanitize user input.

## Best practices

- Keep extension methods focused and specific.
- Avoid adding unrelated utility methods to a broad type.
- Prefer domain-specific names, not generic names like `DoStuff`.
- Use extension methods for convenience, not for replacing proper domain design.

## Summary

Extension methods are a clean way to add reusable support functions without modifying third-party or built-in classes. In real-world code, they help improve readability and reduce repetitive utility logic.
