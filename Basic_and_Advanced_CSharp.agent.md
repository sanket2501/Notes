# Basic and Advanced C# — Notes

## Overview
Concise reference covering Core C# concepts (basic) and advanced topics for experienced developers.

---

## Basic C# Topics

- **Syntax & Types**: value vs reference types, `int`, `string`, `bool`, `decimal`, `enum`, `struct`.
- **Variables & Scope**: local, field, `const`, `readonly`, `var`, nullable value types (`int?`).
- **Operators & Control Flow**: arithmetic, comparison, logical, `if`/`else`, `switch` (pattern matching), `?:`, `??`.
- **Methods & Parameters**: method declarations, `ref`, `out`, `in`, `params`, optional parameters, expression-bodied members.
- **Collections**: arrays, `List<T>`, `Dictionary<TKey,TValue>`, `HashSet<T>`, `Queue<T>`, `Stack<T>`.
- **Generics (intro)**: generic classes/methods, type constraints (`where T : class`, `new()`).
- **Object-Oriented Principles**: classes, structs, constructors, fields, properties, access modifiers (`public`, `private`, `protected`, `internal`).
- **Inheritance & Polymorphism**: `virtual`, `override`, `abstract` classes, `sealed`.
- **Interfaces**: `interface`, explicit interface implementation, default interface methods.
- **Exception Handling**: `try` / `catch` / `finally`, creating custom exceptions, best practices.
- **Delegates & Events**: basic `delegate` types, `Action`, `Func`, event subscription/unsubscription.
- **LINQ (intro)**: query syntax, method syntax (`Where`, `Select`, `OrderBy`, `GroupBy`), deferred execution.
- **Async/Await (intro)**: `Task`, `Task<T>`, `async`/`await`, exception handling in async methods.
- **Basic I/O**: `File`, `Stream`, `StreamReader`/`StreamWriter`, synchronous vs asynchronous file ops.
- **Serialization**: `System.Text.Json` basics, attributes for JSON control, `Newtonsoft.Json` quick notes.

### Short Basic Examples

```csharp
// Method with optional and params
public void Log(string msg, int level = 1, params string[] tags)
{
    Console.WriteLine($"[{level}] {msg} [{string.Join(',', tags)}]");
}

// Simple async
public async Task<int> GetCountAsync()
{
    await Task.Delay(10);
    return 42;
}
```

---

## Advanced C# Topics

- **Advanced Generics**: covariance/contravariance, generic delegates, advanced constraints, generic math (C# 11+).
- **Memory & Span**: `Span<T>`, `ReadOnlySpan<T>`, `Memory<T>`, stackalloc, slicing without allocations.
- **ValueTask and Performance**: when to use `ValueTask<T>`, pooling patterns, minimizing allocations.
- **Advanced Async Patterns**: `IAsyncEnumerable<T>`, channels (`System.Threading.Channels`), cancellation tokens and cooperative cancellation strategies.
- **Reflection & Emission**: `System.Reflection`, `Type` inspection, `MethodInfo.Invoke`, `System.Reflection.Emit` basics, `AssemblyLoadContext`.
- **Expression Trees & LINQ Providers**: building expression trees, writing custom IQueryable providers.
- **Source Generators & Roslyn**: analyzers, generators to reduce boilerplate, best practices for incremental generators.
- **Unsafe Code & Interop**: `unsafe` keyword, pointers, `fixed`, `Marshal` and P/Invoke patterns.
- **Concurrency & Threading**: `ThreadPool`, `Task` scheduling, `Parallel` class, low-level primitives (`SemaphoreSlim`, `ManualResetEventSlim`), lock-free patterns.
- **Synchronization Primitives**: locks (`lock`), `Monitor`, `ReaderWriterLockSlim`, `ConcurrentDictionary`, `ConcurrentQueue`.
- **Design Patterns & Architecture**: repository, unit of work, mediator, CQRS, event sourcing, domain-driven design patterns for C#.
- **Advanced DI & Composition**: custom service lifetimes, scope design, factory patterns in DI, `IServiceProvider` advanced usage.
- **Profiling & Diagnostics**: `dotnet-trace`, `dotnet-counters`, ETW, DiagnosticSource/Activity, logging correlation, metrics.
- **Interop with Native & COM**: `DllImport`, COM interop basics, marshaling strategies.
- **Security & Cryptography**: `System.Security.Cryptography`, secure string handling, data protection API.
- **Blazor / ASP.NET Core Advanced**: middleware pipelines, endpoint routing, advanced model binding, streaming responses.
- **Testing & Mocks**: advanced unit testing patterns, integration tests, test doubles, property-based testing.

### Advanced Code Snippets

```csharp
// Using Span<T> to parse without allocations
public int SumBytes(ReadOnlySpan<byte> data)
{
    int sum = 0;
    foreach (var b in data)
        sum += b;
    return sum;
}

// IAsyncEnumerable example
public async IAsyncEnumerable<int> StreamNumbers([EnumeratorCancellation] CancellationToken ct = default)
{
    for (int i = 0; i < 10; i++)
    {
        ct.ThrowIfCancellationRequested();
        await Task.Delay(100, ct);
        yield return i;
    }
}
```

---

## Study & Practice Suggestions

- Start with core language and small console apps. Practice OOP and collections.
- Learn LINQ and async patterns through practical exercises (API callers, file processing).
- Move to advanced topics: read how `Span<T>` avoids allocations, write a source generator, and profile an app to find allocations.
- Build a small web service with ASP.NET Core, add EF Core, logging, tests, and CI.

---

## References

- Microsoft C# Guide: https://learn.microsoft.com/dotnet/csharp/
- .NET API Browser: https://learn.microsoft.com/dotnet/api/
- Roslyn and Source Generators docs on Microsoft Learn.

---

_File: Basic and Advanced C# notes — concise reference._
