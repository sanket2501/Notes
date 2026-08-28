# 02. Exception Handling

Tags: dotnet

Exception handling is a mechanism for managing runtime errors and keeping the application stable. In real systems, exceptions can come from invalid input, missing files, database failures, network issues, or impossible object states. Good exception handling does not hide bugs; it makes failure predictable and recoverable.

## Table of Contents

- [Basic concept](#basic-concept)
- [Try, catch, finally](#try-catch-finally)
- [Throwing exceptions](#throwing-exceptions)
- [Custom exceptions](#custom-exceptions)
- [Exception filters](#exception-filters)
- [AggregateException](#aggregateexception)
- [Real-world examples](#real-world-examples)

## Basic concept

The runtime throws exceptions when something unexpected occurs. You can handle those exceptions by using `try`, `catch`, and `finally` blocks.

```csharp
try
{
    int value = int.Parse("abc");
}
catch (FormatException ex)
{
    Console.WriteLine($"Invalid number: {ex.Message}");
}
finally
{
    Console.WriteLine("This always runs");
}
```

## Try, catch, finally

- `try`: contains code that may fail.
- `catch`: handles the exception.
- `finally`: runs regardless of success or failure.

```csharp
public class PaymentService
{
    public void Process(decimal amount)
    {
        try
        {
            if (amount <= 0)
                throw new ArgumentOutOfRangeException(nameof(amount));

            Console.WriteLine($"Processing payment: {amount}");
        }
        catch (ArgumentOutOfRangeException ex)
        {
            Console.WriteLine($"Invalid payment amount: {ex.Message}");
        }
        finally
        {
            Console.WriteLine("Payment attempt completed");
        }
    }
}
```

## Throwing exceptions

Use `throw` when a method cannot continue safely.

```csharp
public class UserRegistrationService
{
    public void Register(string username)
    {
        if (string.IsNullOrWhiteSpace(username))
            throw new ArgumentException("Username is required", nameof(username));

        Console.WriteLine($"User {username} registered");
    }
}
```

This is helpful when inputs are invalid or the object is in an impossible state.

## Custom exceptions

Create custom exceptions when the app has domain-specific failure modes.

```csharp
using System;

public class OrderValidationException : Exception
{
    public OrderValidationException(string message) : base(message)
    {
    }
}

public class OrderService
{
    public void ValidateOrder(string orderId)
    {
        if (string.IsNullOrWhiteSpace(orderId))
            throw new OrderValidationException("Order ID cannot be empty");
    }
}
```

This improves code clarity because callers can catch business errors specifically.

## Exception filters

Exception filters allow you to catch only specific cases.

```csharp
try
{
    // risky code
    ThrowSpecificError();
}
catch (Exception ex) when (ex.Message.Contains("timeout"))
{
    Console.WriteLine("A timeout occurred. Please retry.");
}
catch (Exception ex)
{
    Console.WriteLine($"Unexpected error: {ex.Message}");
}
```

This prevents broad exception handling from swallowing unrelated problems.

## AggregateException

When working with multiple asynchronous operations, more than one exception may occur. `AggregateException` helps you inspect all of them.

```csharp
using System;
using System.Threading.Tasks;

public class Example
{
    public static async Task RunAsync()
    {
        try
        {
            await Task.WhenAll(
                Task.Run(() => throw new InvalidOperationException("Task 1 failed")),
                Task.Run(() => throw new TimeoutException("Task 2 timed out"))
            );
        }
        catch (AggregateException ex)
        {
            foreach (var inner in ex.InnerExceptions)
            {
                Console.WriteLine(inner.Message);
            }
        }
    }
}
```

This is common in parallel workflows, background jobs, and batch processing.

## Real-world examples

### Example 1: API request validation

```csharp
public class UserController
{
    public void CreateUser(string email)
    {
        try
        {
            if (string.IsNullOrWhiteSpace(email))
                throw new ArgumentException("Email is required");

            if (!email.Contains("@"))
                throw new FormatException("Email format is invalid");

            Console.WriteLine("User created successfully");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to create user: {ex.Message}");
        }
    }
}
```

### Example 2: reading a file

```csharp
public class FileReader
{
    public string Read(string path)
    {
        try
        {
            return File.ReadAllText(path);
        }
        catch (FileNotFoundException ex)
        {
            Console.WriteLine($"File not found: {ex.FileName}");
            return string.Empty;
        }
        catch (UnauthorizedAccessException ex)
        {
            Console.WriteLine($"Access denied: {ex.Message}");
            return string.Empty;
        }
    }
}
```

## Best practices

- Catch specific exceptions, not just `Exception`.
- Do not swallow exceptions silently.
- Log errors with enough detail to diagnose root causes.
- Use `finally` for cleanup such as closing resources.
- Throw domain-specific exceptions when business rules fail.

## Summary

Exception handling is not just about preventing crashes. It is about converting low-level failures into meaningful, actionable application behavior. In production code, a clear exception strategy is essential for reliability and maintainability.
