# 18. Dependency Injection

Dependency Injection (DI) is a design pattern used to implement Inversion of Control (IoC). It allows a class to receive its dependencies from an external source rather than creating them itself.

## Why Use DI?

- To reduce tight coupling between classes.
- To improve testability (e.g., easier to mock services).
- To follow SOLID principles (especially the Dependency Inversion Principle).
  - High-level modules should not depend on low-level modules. Both should depend on abstractions.
  - Abstractions should not depend on details. Details should depend on abstractions.
- To promote reuse and flexibility.

## 1. Service (Dependency)

The object that a class depends on.

```csharp
public interface IMessageService
{
    void Send(string message);
}

public class EmailService : IMessageService
{
    public void Send(string message)
    {
        Console.WriteLine($"Email sent: {message}");
    }
}
```

## 2. Client (Dependent class)

The class that requires the service.

```csharp
public class Notification
{
    private readonly IMessageService _messageService;

    public Notification(IMessageService messageService)
    {
        _messageService = messageService;
    }

    public void Alert(string message)
    {
        _messageService.Send(message);
    }
}
```

## 3. Injector / IoC Container

### Step 1: Create a Static Class

```csharp
// File: IoCConfig.cs or DependencyInjectionConfig.cs
public static class IoCConfig
{
    public static void RegisterServices(IServiceCollection services)
    {
        // Register your services here
        services.AddScoped<IMessageService, EmailService>();
        services.AddScoped<NotificationService>();
        services.AddSingleton<ILoggingService, FileLoggingService>();
    }
}
```

### Step 2: Call It in Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

// Call your custom IoC method
IoCConfig.RegisterServices(builder.Services);

var app = builder.Build();
```

If your project is layered (e.g., Controllers, Services, Repositories), your `IoCConfig.cs` can register by section:

```csharp
public static class IoCConfig
{
    public static void RegisterServices(IServiceCollection services)
    {
        RegisterRepositories(services);
        RegisterServicesLayer(services);
    }

    private static void RegisterRepositories(IServiceCollection services)
    {
        services.AddScoped<IUserRepository, UserRepository>();
    }

    private static void RegisterServicesLayer(IServiceCollection services)
    {
        services.AddScoped<IUserService, UserService>();
    }
}
```

## Task: How to Do It

- Create Config File: `IoCConfig.cs` static class
- Register Services: Use `services.AddScoped`, `AddSingleton`, etc.
- Use `IServiceCollection` in your custom IoC method to register everything.
- Keep it modular — break into methods by layer or feature if needed.
- Prefer interfaces for registrations (`IService` → `Service`).
- Call in `Program.cs`: `IoCConfig.RegisterServices(builder.Services);`

## What is IServiceCollection?

`IServiceCollection` is an interface provided by .NET Core that acts as a container to register services your application will use.
It is part of the `Microsoft.Extensions.DependencyInjection` namespace and is used to build and configure the DI container.
It is a collection of service registrations that you add to during app startup, telling the framework what classes to inject, how to create them, and how long they should live.

```csharp
var builder = WebApplication.CreateBuilder(args);

// Accessing IServiceCollection
IServiceCollection services = builder.Services;

// Registering services
services.AddTransient<IMessageService, EmailService>();
services.AddScoped<NotificationService>();
services.AddSingleton<ILoggingService, FileLoggingService>();

var app = builder.Build();
```

## Built-in DI Container in .NET Core

### Service Lifetime Options

- Transient: New instance every time. `AddTransient<IService, Service>()`
- Scoped: Same instance per HTTP request. `AddScoped<IService, Service>()`
- Singleton: One instance for entire app. `AddSingleton<IService, Service>()`

## Types of Dependency Injection

1. Constructor Injection (Most Common)

Dependencies are provided via the constructor.

```csharp
public class MyClass
{
    private readonly IService _service;

    public MyClass(IService service)
    {
        _service = service;
    }
}
```

2. Property Injection

Dependencies are set via public properties.

```csharp
public class MyClass
{
    public IService Service { get; set; }
}
```

3. Method Injection

Dependencies are passed as method parameters.

```csharp
public class MyClass
{
    public void PerformAction(IService service)
    {
        service.DoWork();
    }
}
```
