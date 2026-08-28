# 07. Async Programming

Tags: dotnet

Asynchronous programming allows code to keep running without blocking the calling thread. This is especially important in web applications, file processing, API calls, and background services where the app must remain responsive while waiting for I/O work.

## Table of Contents

- [Why async matters](#why-async-matters)
- [Async and await](#async-and-await)
- [Task and Task<T>](#task-and-taskt)
- [Task.Run](#taskrun)
- [Task.WhenAll and Task.WhenAny](#taskwhenall-and-taskwhenany)
- [ConfigureAwait](#configureawait)
- [Real-world examples](#real-world-examples)

## Why async matters

Synchronous code blocks the main thread while waiting for work to finish. In user interfaces or web APIs, this causes poor responsiveness and slower throughput.

```csharp
public static void DownloadSync()
{
    Thread.Sleep(5000); // blocks the thread
    Console.WriteLine("Download finished");
}
```

Async alternative:

```csharp
public static async Task DownloadAsync()
{
    await Task.Delay(5000);
    Console.WriteLine("Download finished");
}
```

## Async and await

Use `async` on methods that perform asynchronous work and `await` to wait for the completion of a task.

```csharp
public static async Task<string> FetchDataAsync()
{
    await Task.Delay(2000);
    return "Data loaded";
}

public static async Task Main()
{
    string result = await FetchDataAsync();
    Console.WriteLine(result);
}
```

Rules:

- An async method should return `Task`, `Task<T>`, or `void` in event handlers.
- `await` is only valid inside an `async` method.
- Do not use `async void` except for UI event handlers.

## Task and Task<T>

`Task` represents an asynchronous operation without a result.

```csharp
public static async Task ProcessAsync()
{
    await Task.Delay(1000);
    Console.WriteLine("Processing complete");
}
```

`Task<T>` returns a value.

```csharp
public static async Task<int> GetCountAsync()
{
    await Task.Delay(1000);
    return 42;
}
```

## Task.Run

`Task.Run` is commonly used for CPU-bound work on a thread pool.

```csharp
public static async Task RunHeavyWorkAsync()
{
    var result = await Task.Run(() =>
    {
        int total = 0;
        for (int i = 0; i < 1000000; i++)
            total += i;
        return total;
    });

    Console.WriteLine($"Total: {result}");
}
```

## Task.WhenAll and Task.WhenAny

Use `Task.WhenAll` when you want to wait for multiple tasks to complete.

```csharp
public static async Task ExecuteParallelAsync()
{
    Task first = Task.Delay(2000);
    Task second = Task.Delay(3000);

    await Task.WhenAll(first, second);
    Console.WriteLine("Both tasks completed");
}
```

Use `Task.WhenAny` when you only care about the first task finishing.

```csharp
public static async Task RaceAsync()
{
    Task fast = Task.Delay(1000);
    Task slow = Task.Delay(5000);

    Task completed = await Task.WhenAny(fast, slow);
    Console.WriteLine("One task finished first");
}
```

This is useful for timeout-based or first-response patterns.

## ConfigureAwait

`ConfigureAwait(false)` tells the runtime not to capture the original synchronization context. This is common in library code.

```csharp
public static async Task ReadDataAsync()
{
    await Task.Delay(1000).ConfigureAwait(false);
    Console.WriteLine("Completed without capturing context");
}
```

## Real-world examples

### Example 1: fetching multiple API responses

```csharp
public static async Task DownloadReportsAsync()
{
    Task<string> salesTask = DownloadAsync("sales");
    Task<string> inventoryTask = DownloadAsync("inventory");

    await Task.WhenAll(salesTask, inventoryTask);

    Console.WriteLine(await salesTask);
    Console.WriteLine(await inventoryTask);
}

public static async Task<string> DownloadAsync(string name)
{
    await Task.Delay(1000);
    return $"{name} report downloaded";
}
```

### Example 2: safe file processing pipeline

```csharp
public static async Task ProcessFilesAsync(string[] files)
{
    foreach (var file in files)
    {
        await Task.Run(() =>
        {
            // expensive file processing
            Console.WriteLine($"Processing {file}");
        });
    }
}
```

## Best practices

- Use async only for real asynchronous work.
- Avoid blocking with `Task.Wait()` or `Thread.Sleep()` in async code.
- Use cancellation tokens for long-running operations.
- Keep async methods on the hot path efficient and predictable.

## Summary

Async programming is essential in modern .NET development. It helps applications stay responsive, handle multiple I/O operations efficiently, and scale better under real-world workloads.
